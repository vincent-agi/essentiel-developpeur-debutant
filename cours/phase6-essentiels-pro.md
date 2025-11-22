# Phase 6 — Essentiels Pro (Junior Opérationnel)

## 🎯 Objectif
Maîtriser les concepts qu'un junior rencontrera **dès les premiers mois en entreprise** pour être immédiatement productif.

**Cette phase est SIMPLIFIÉE par rapport aux Enterprise Integration Patterns** pour se concentrer sur l'opérationnalité immédiate.

---

## 1. Gestion d'État Avancée (State Management)

### 📚 Théorie Fondamentale
Dans les applications complexes, plusieurs composants ont besoin d'accéder aux mêmes données. Le state management centralise ces données et leur logique.

### 🖥️ Ce qui se passe
Les données sont stockées en mémoire (RAM) dans un service singleton. Les composants s'abonnent aux changements via des observables ou signals.

### 💻 Solution Angular 20 : Signals

#### core/state/cart.state.ts
```typescript
import { Injectable, signal, computed } from '@angular/core';

export interface CartItem {
  productId: string;
  name: string;
  price: number;
  quantity: number;
}

@Injectable({ providedIn: 'root' })
export class CartState {
  // State privé
  private itemsSignal = signal<CartItem[]>([]);

  // State publics (readonly)
  items = this.itemsSignal.asReadonly();
  
  // Computed values (dérivés automatiquement)
  total = computed(() => 
    this.itemsSignal().reduce((sum, item) => sum + item.price * item.quantity, 0)
  );

  itemCount = computed(() => 
    this.itemsSignal().reduce((sum, item) => sum + item.quantity, 0)
  );

  isEmpty = computed(() => this.itemsSignal().length === 0);

  // Actions (mutations)
  addItem(item: CartItem) {
    this.itemsSignal.update(current => {
      const existing = current.find(i => i.productId === item.productId);
      
      if (existing) {
        return current.map(i => 
          i.productId === item.productId 
            ? { ...i, quantity: i.quantity + item.quantity }
            : i
        );
      }
      
      return [...current, item];
    });
  }

  removeItem(productId: string) {
    this.itemsSignal.update(current => 
      current.filter(i => i.productId !== productId)
    );
  }

  updateQuantity(productId: string, quantity: number) {
    if (quantity <= 0) {
      this.removeItem(productId);
      return;
    }

    this.itemsSignal.update(current =>
      current.map(i => 
        i.productId === productId 
          ? { ...i, quantity }
          : i
      )
    );
  }

  clear() {
    this.itemsSignal.set([]);
  }
}
```

### Utilisation dans un composant
```typescript
import { Component, inject } from '@angular/core';
import { CartState } from './core/state/cart.state';

@Component({
  selector: 'app-cart',
  template: `
    <div class="cart">
      <h2>Panier ({{ cartState.itemCount() }} articles)</h2>
      
      @for (item of cartState.items(); track item.productId) {
        <div class="cart-item">
          <span>{{ item.name }}</span>
          <input 
            type="number" 
            [value]="item.quantity"
            (change)="updateQuantity(item.productId, $event)">
          <span>{{ item.price * item.quantity | currency: 'EUR' }}</span>
          <button (click)="cartState.removeItem(item.productId)">×</button>
        </div>
      }

      <div class="total">
        Total: {{ cartState.total() | currency: 'EUR' }}
      </div>
    </div>
  `
})
export class CartComponent {
  cartState = inject(CartState);

  updateQuantity(productId: string, event: any) {
    const quantity = parseInt(event.target.value, 10);
    this.cartState.updateQuantity(productId, quantity);
  }
}
```

### 🔴 Erreurs Courantes

| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Muter directement le signal | Change detection ne se déclenche pas | Toujours utiliser `.update()` ou `.set()` |
| Ne pas utiliser `asReadonly()` | Composants peuvent modifier le state | Exposer uniquement des versions readonly |
| Computed trop complexes | Performance dégradée | Décomposer en plusieurs computed simples |

---

## 2. Authentification JWT (Cas Réel)

### 📚 Théorie Fondamentale
JWT (JSON Web Token) est un standard pour transmettre des informations de manière sécurisée. Le token contient les données utilisateur et est signé par le serveur.

### 🖥️ Ce qui se passe
1. **Login** : Serveur valide identifiants → génère JWT → renvoie au client
2. **Stockage** : Client stocke le JWT (localStorage/sessionStorage)
3. **Requêtes** : Client envoie JWT dans header `Authorization: Bearer <token>`
4. **Validation** : Serveur vérifie la signature du JWT

### 💻 Backend NestJS

#### Installation
```bash
npm install @nestjs/jwt @nestjs/passport passport passport-jwt
npm install -D @types/passport-jwt
```

#### auth/auth.module.ts
```typescript
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { AuthService } from './auth.service';
import { AuthController } from './auth.controller';
import { JwtStrategy } from './jwt.strategy';

@Module({
  imports: [
    JwtModule.register({
      secret: 'YOUR_SECRET_KEY', // En prod: process.env.JWT_SECRET
      signOptions: { expiresIn: '24h' },
    }),
  ],
  controllers: [AuthController],
  providers: [AuthService, JwtStrategy],
})
export class AuthModule {}
```

#### auth/auth.service.ts
```typescript
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';

interface User {
  id: string;
  email: string;
  password: string; // En prod: hashé avec bcrypt
  name: string;
}

@Injectable()
export class AuthService {
  // En prod: récupérer depuis la DB
  private users: User[] = [
    { id: '1', email: 'admin@example.com', password: 'Password123', name: 'Admin' }
  ];

  constructor(private jwtService: JwtService) {}

  async login(email: string, password: string) {
    const user = this.users.find(u => u.email === email);
    
    if (!user || user.password !== password) {
      throw new UnauthorizedException('Identifiants invalides');
    }

    const payload = { 
      sub: user.id, 
      email: user.email,
      name: user.name 
    };

    return {
      access_token: this.jwtService.sign(payload),
      user: {
        id: user.id,
        email: user.email,
        name: user.name
      }
    };
  }

  async validateToken(payload: any): Promise<User> {
    const user = this.users.find(u => u.id === payload.sub);
    if (!user) {
      throw new UnauthorizedException();
    }
    return user;
  }
}
```

#### auth/jwt.strategy.ts
```typescript
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { AuthService } from './auth.service';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: 'YOUR_SECRET_KEY',
    });
  }

  async validate(payload: any) {
    return this.authService.validateToken(payload);
  }
}
```

#### auth/jwt-auth.guard.ts
```typescript
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

#### Utilisation dans un contrôleur
```typescript
import { Controller, Get, UseGuards } from '@nestjs/common';
import { JwtAuthGuard } from './auth/jwt-auth.guard';

@Controller('tasks')
export class TasksController {
  @Get()
  @UseGuards(JwtAuthGuard) // ✅ Route protégée
  findAll() {
    return this.tasksService.findAll();
  }
}
```

### 💻 Frontend Angular

#### core/services/auth.service.ts
```typescript
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Router } from '@angular/router';
import { tap } from 'rxjs/operators';

interface LoginResponse {
  access_token: string;
  user: {
    id: string;
    email: string;
    name: string;
  };
}

@Injectable({ providedIn: 'root' })
export class AuthService {
  private http = inject(HttpClient);
  private router = inject(Router);

  currentUser = signal<LoginResponse['user'] | null>(null);
  isAuthenticated = signal(false);

  constructor() {
    // Restaurer la session au démarrage
    const token = localStorage.getItem('access_token');
    const user = localStorage.getItem('current_user');
    
    if (token && user) {
      this.currentUser.set(JSON.parse(user));
      this.isAuthenticated.set(true);
    }
  }

  login(email: string, password: string) {
    return this.http.post<LoginResponse>('http://localhost:3000/auth/login', { email, password })
      .pipe(
        tap(response => {
          // ⚠️ ATTENTION SÉCURITÉ : localStorage est vulnérable aux attaques XSS
          // Pour la production, préférer httpOnly cookies (configurés côté backend)
          // Voir table "Erreurs Courantes Auth" pour les alternatives sécurisées
          localStorage.setItem('access_token', response.access_token);
          localStorage.setItem('current_user', JSON.stringify(response.user));
          this.currentUser.set(response.user);
          this.isAuthenticated.set(true);
          this.router.navigate(['/tasks']);
        })
      );
  }

  logout() {
    localStorage.removeItem('access_token');
    localStorage.removeItem('current_user');
    this.currentUser.set(null);
    this.isAuthenticated.set(false);
    this.router.navigate(['/login']);
  }
}
```

#### core/interceptors/auth.interceptor.ts
```typescript
import { HttpInterceptorFn } from '@angular/common/http';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = localStorage.getItem('access_token');
  
  if (token && !req.url.includes('/auth/login')) {
    req = req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    });
  }
  
  return next(req);
};
```

#### core/guards/auth.guard.ts
```typescript
import { inject } from '@angular/core';
import { Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const authGuard = () => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isAuthenticated()) {
    return true;
  }

  router.navigate(['/login']);
  return false;
};
```

#### Utilisation dans les routes
```typescript
import { Routes } from '@angular/router';
import { authGuard } from './core/guards/auth.guard';

export const routes: Routes = [
  { path: 'login', component: LoginComponent },
  { 
    path: 'tasks', 
    component: TaskListComponent,
    canActivate: [authGuard] // ✅ Route protégée
  },
  { path: '', redirectTo: '/tasks', pathMatch: 'full' }
];
```

### 🔴 Erreurs Courantes Auth

| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Stocker le token en clair dans localStorage | Vol de session via XSS | Utiliser httpOnly cookies (backend) + SameSite |
| Ne pas gérer l'expiration du token | Utilisateur déconnecté brutalement | Implémenter refresh token + redirection |
| Mot de passe en clair dans la DB | Fuite de données catastrophique | **Toujours hasher avec bcrypt** |
| Secret JWT faible | Token facilement forgeable | Utiliser secret long et aléatoire (env variable) |

---

## 3. Gestion d'Erreurs Centralisée

### 📚 Théorie Fondamentale
Gérer les erreurs à un seul endroit évite la duplication de code et assure une expérience utilisateur cohérente.

### 💻 Frontend : Error Interceptor

#### core/interceptors/error.interceptor.ts
```typescript
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { inject } from '@angular/core';
import { catchError, throwError } from 'rxjs';
import { Router } from '@angular/router';

export const errorInterceptor: HttpInterceptorFn = (req, next) => {
  const router = inject(Router);

  return next(req).pipe(
    catchError((error: HttpErrorResponse) => {
      let message = 'Une erreur est survenue';

      if (error.error instanceof ErrorEvent) {
        // Erreur client (réseau, etc.)
        message = `Erreur: ${error.error.message}`;
      } else {
        // Erreur serveur
        switch (error.status) {
          case 400:
            message = 'Données invalides';
            break;
          case 401:
            message = 'Non authentifié';
            localStorage.removeItem('access_token');
            router.navigate(['/login']);
            break;
          case 403:
            message = 'Accès interdit';
            break;
          case 404:
            message = 'Ressource non trouvée';
            break;
          case 500:
            message = 'Erreur serveur. Réessayez plus tard.';
            break;
          default:
            message = error.error?.message || message;
        }
      }

      console.error('HTTP Error:', error);
      
      // ⚠️ NOTE : alert() est utilisé ici pour la simplicité pédagogique
      // En production, utiliser un service de notifications professionnel :
      // - Angular Material Snackbar: https://material.angular.io/components/snack-bar
      // - ngx-toastr: https://www.npmjs.com/package/ngx-toastr
      // Exemple : this.toastr.error(message, 'Erreur');
      alert(message);
      
      return throwError(() => error);
    })
  );
};
```

### 💻 Backend : Exception Filter

#### common/filters/http-exception.filter.ts
```typescript
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
} from '@nestjs/common';
import { Response } from 'express';

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();

    let status = HttpStatus.INTERNAL_SERVER_ERROR;
    let message = 'Internal server error';

    if (exception instanceof HttpException) {
      status = exception.getStatus();
      const exceptionResponse = exception.getResponse();
      message = typeof exceptionResponse === 'string' 
        ? exceptionResponse 
        : (exceptionResponse as any).message;
    }

    console.error('Exception caught:', exception);

    response.status(status).json({
      statusCode: status,
      message,
      timestamp: new Date().toISOString(),
    });
  }
}
```

#### Enregistrer globalement dans main.ts
```typescript
import { AllExceptionsFilter } from './common/filters/http-exception.filter';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new AllExceptionsFilter()); // ✅
  // ...
}
```

---

## 4. Tests (Essentiel pour un Junior)

### 📚 Théorie Fondamentale
Les tests automatisés garantissent que le code fonctionne comme prévu et évitent les régressions.

### 💻 Tests Unitaires NestJS

#### tasks.service.spec.ts
```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { TasksService } from './tasks.service';
import { TaskStatus } from './dto/create-task.dto';

describe('TasksService', () => {
  let service: TasksService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [TasksService],
    }).compile();

    service = module.get<TasksService>(TasksService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  it('should create a task', () => {
    const task = service.create({
      title: 'Test Task',
      description: 'Test Description',
      status: TaskStatus.TODO,
    });

    expect(task.id).toBeDefined();
    expect(task.title).toBe('Test Task');
    expect(task.status).toBe(TaskStatus.TODO);
  });

  it('should find all tasks', () => {
    service.create({ title: 'Task 1', status: TaskStatus.TODO });
    service.create({ title: 'Task 2', status: TaskStatus.DONE });

    const tasks = service.findAll();
    expect(tasks).toHaveLength(2);
  });

  it('should throw NotFoundException when task not found', () => {
    expect(() => service.findOne('999')).toThrow('Task with ID 999 not found');
  });
});
```

### 💻 Tests Composants Angular

#### task-list.component.spec.ts
```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { TaskListComponent } from './task-list.component';
import { TaskService } from '../../../core/services/task.service';
import { of } from 'rxjs';

describe('TaskListComponent', () => {
  let component: TaskListComponent;
  let fixture: ComponentFixture<TaskListComponent>;
  let taskService: jasmine.SpyObj<TaskService>;

  beforeEach(async () => {
    const taskServiceSpy = jasmine.createSpyObj('TaskService', ['getTasks']);

    await TestBed.configureTestingModule({
      imports: [TaskListComponent],
      providers: [
        { provide: TaskService, useValue: taskServiceSpy }
      ]
    }).compileComponents();

    taskService = TestBed.inject(TaskService) as jasmine.SpyObj<TaskService>;
    fixture = TestBed.createComponent(TaskListComponent);
    component = fixture.componentInstance;
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should load tasks on init', () => {
    const mockTasks = [
      { id: '1', title: 'Test Task', description: '', status: 'TODO', createdAt: new Date() }
    ];
    taskService.getTasks.and.returnValue(of(mockTasks));

    fixture.detectChanges(); // Trigger ngOnInit

    expect(component.tasks()).toEqual(mockTasks);
    expect(component.loading()).toBeFalse();
  });
});
```

### Lancer les tests
```bash
# NestJS
npm run test

# Angular
ng test
```

---

## 5. Déploiement Basique

### 📚 Théorie Fondamentale
Le déploiement consiste à rendre l'application accessible sur Internet.

### 💻 Backend NestJS

#### Option 1 : Render.com (gratuit)
1. Créer compte sur https://render.com
2. Connecter le repo GitHub
3. Créer un "Web Service"
4. Build Command: `npm install && npm run build`
5. Start Command: `node dist/main.js`
6. Variables d'environnement : `JWT_SECRET`, `DATABASE_URL`, etc.

#### Option 2 : Railway.app
```bash
npm i -g @railway/cli
railway login
railway init
railway up
```

### 💻 Frontend Angular

#### Option 1 : Netlify
```bash
ng build --configuration production
# Upload dossier dist/frontend-tasks/browser sur Netlify
```

#### Option 2 : Vercel
```bash
npm i -g vercel
vercel
```

### Configuration pour production
#### environment.prod.ts
```typescript
export const environment = {
  production: true,
  apiUrl: 'https://your-backend.render.com'
};
```

---

## ✅ Checklist Phase 6

Après cette phase, je suis capable de :
- [ ] Gérer un state global avec signals
- [ ] Implémenter l'authentification JWT (backend + frontend)
- [ ] Protéger des routes avec guards
- [ ] Gérer les erreurs de manière centralisée
- [ ] Écrire des tests unitaires (backend + frontend)
- [ ] Déployer une application en production

---

## 🎯 Projet Final : Application Complète Production-Ready

Créer une application de gestion de projets avec :
1. ✅ Authentification JWT
2. ✅ CRUD complet
3. ✅ State management avec signals
4. ✅ Gestion d'erreurs centralisée
5. ✅ Tests unitaires (couverture > 70%)
6. ✅ Déployée en production

---

## 📚 Ressources

- [Angular Signals](https://angular.dev/guide/signals)
- [JWT Best Practices](https://auth0.com/blog/a-look-at-the-latest-draft-for-jwt-bcp/)
- [Testing Angular](https://angular.dev/guide/testing)
- [Testing NestJS](https://docs.nestjs.com/fundamentals/testing)
- [Deploying to Netlify](https://docs.netlify.com/frameworks/angular/)
