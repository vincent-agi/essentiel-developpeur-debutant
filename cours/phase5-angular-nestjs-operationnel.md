# Phase 5 — Développement Full-Stack Angular 20 + NestJS (Opérationnel)

## 🎯 Objectif
Construire une **application CRUD complète** (Gestion de Tâches) en suivant un parcours guidé étape par étape pour devenir opérationnel rapidement.

**Durée estimée** : 3 semaines
**Projet fil rouge** : Application de gestion de tâches (To-Do App) avec authentification

---

## 📋 Architecture du Projet

```
projet-tasks/
├── backend/ (NestJS)
│   ├── src/
│   │   ├── auth/
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── jwt.strategy.ts
│   │   │   └── dto/
│   │   ├── tasks/
│   │   │   ├── tasks.module.ts
│   │   │   ├── tasks.controller.ts
│   │   │   ├── tasks.service.ts
│   │   │   ├── task.entity.ts
│   │   │   └── dto/
│   │   └── main.ts
│   └── package.json
└── frontend/ (Angular 20)
    ├── src/app/
    │   ├── core/
    │   │   ├── services/
    │   │   ├── interceptors/
    │   │   └── guards/
    │   ├── features/
    │   │   ├── auth/
    │   │   └── tasks/
    │   └── shared/
    └── package.json
```

---

## Étape 1 : Setup Backend NestJS

### 🖥️ Ce qui se passe
Node.js exécute un serveur HTTP qui écoute sur un port (ex: 3000). À chaque requête HTTP, NestJS route vers le bon contrôleur.

### Installation
```bash
npm i -g @nestjs/cli
nest new backend-tasks
cd backend-tasks
```

### Générer le module Tasks
```bash
nest generate module tasks
nest generate controller tasks
nest generate service tasks
```

### Créer les DTOs (Data Transfer Objects)

#### tasks/dto/create-task.dto.ts
```typescript
import { IsString, IsNotEmpty, IsEnum, IsOptional } from 'class-validator';

export enum TaskStatus {
  TODO = 'TODO',
  IN_PROGRESS = 'IN_PROGRESS',
  DONE = 'DONE'
}

export class CreateTaskDto {
  @IsString()
  @IsNotEmpty()
  title: string;

  @IsString()
  @IsOptional()
  description?: string;

  @IsEnum(TaskStatus)
  status: TaskStatus;
}
```

#### tasks/dto/update-task.dto.ts
```typescript
import { PartialType } from '@nestjs/mapped-types';
import { CreateTaskDto } from './create-task.dto';

export class UpdateTaskDto extends PartialType(CreateTaskDto) {}
```

### Implémenter le Service

#### tasks/tasks.service.ts
```typescript
import { Injectable, NotFoundException } from '@nestjs/common';
import { CreateTaskDto } from './dto/create-task.dto';
import { UpdateTaskDto } from './dto/update-task.dto';

interface Task {
  id: string;
  title: string;
  description: string;
  status: string;
  createdAt: Date;
}

@Injectable()
export class TasksService {
  private tasks: Task[] = [];
  private idCounter = 1;

  findAll(): Task[] {
    return this.tasks;
  }

  findOne(id: string): Task {
    const task = this.tasks.find(t => t.id === id);
    if (!task) {
      throw new NotFoundException(`Task with ID ${id} not found`);
    }
    return task;
  }

  create(createTaskDto: CreateTaskDto): Task {
    const task: Task = {
      id: String(this.idCounter++),
      ...createTaskDto,
      description: createTaskDto.description || '',
      createdAt: new Date(),
    };
    this.tasks.push(task);
    return task;
  }

  update(id: string, updateTaskDto: UpdateTaskDto): Task {
    const task = this.findOne(id);
    Object.assign(task, updateTaskDto);
    return task;
  }

  remove(id: string): void {
    const index = this.tasks.findIndex(t => t.id === id);
    if (index === -1) {
      throw new NotFoundException(`Task with ID ${id} not found`);
    }
    this.tasks.splice(index, 1);
  }
}
```

### Implémenter le Contrôleur

#### tasks/tasks.controller.ts
```typescript
import {
  Controller,
  Get,
  Post,
  Body,
  Patch,
  Param,
  Delete,
} from '@nestjs/common';
import { TasksService } from './tasks.service';
import { CreateTaskDto } from './dto/create-task.dto';
import { UpdateTaskDto } from './dto/update-task.dto';

@Controller('tasks')
export class TasksController {
  constructor(private readonly tasksService: TasksService) {}

  @Get()
  findAll() {
    return this.tasksService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.tasksService.findOne(id);
  }

  @Post()
  create(@Body() createTaskDto: CreateTaskDto) {
    return this.tasksService.create(createTaskDto);
  }

  @Patch(':id')
  update(@Param('id') id: string, @Body() updateTaskDto: UpdateTaskDto) {
    return this.tasksService.update(id, updateTaskDto);
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    this.tasksService.remove(id);
    return { message: 'Task deleted successfully' };
  }
}
```

### Activer la Validation et CORS

#### main.ts
```typescript
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Activer la validation automatique des DTOs
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,      // Supprime les propriétés non définies dans le DTO
    forbidNonWhitelisted: true, // Rejette les requêtes avec propriétés inconnues
    transform: true,      // Transforme les types automatiquement
  }));

  // Activer CORS pour permettre les appels depuis Angular
  app.enableCors({
    origin: 'http://localhost:4200',
    credentials: true,
  });

  await app.listen(3000);
  console.log('🚀 Backend NestJS running on http://localhost:3000');
}
bootstrap();
```

### Installer les dépendances de validation
```bash
npm install class-validator class-transformer
```

### Tester le backend
```bash
npm run start:dev

# Dans un autre terminal
curl http://localhost:3000/tasks
# []

curl -X POST http://localhost:3000/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Ma première tâche","status":"TODO"}'
# {"id":"1","title":"Ma première tâche","description":"","status":"TODO","createdAt":"..."}
```

### 🔴 Erreurs Courantes Backend

| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Oublier `class-validator` | Validation échoue silencieusement | `npm i class-validator class-transformer` |
| Ne pas activer `ValidationPipe` | DTOs non validés | Ajouter dans `main.ts` |
| CORS non configuré | Frontend bloqué par le navigateur | `app.enableCors()` dans `main.ts` |
| Oublier `@Injectable()` sur service | `Nest can't resolve dependencies` | Ajouter le décorateur |
| Port 3000 déjà utilisé | Erreur au démarrage | Changer le port ou tuer le processus existant |

---

## Étape 2 : Setup Frontend Angular 20

### Installation
```bash
npm i -g @angular/cli
ng new frontend-tasks
cd frontend-tasks
```

Lors de l'installation, choisir :
- **Routing** : Yes
- **Stylesheet** : SCSS

### Générer les composants et services
```bash
# Service pour communiquer avec l'API
ng generate service core/services/task

# Composants pour les tâches
ng generate component features/tasks/task-list
ng generate component features/tasks/task-form
ng generate component features/tasks/task-item
```

### Configurer HttpClient

#### app.config.ts
```typescript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient(), // ✅ Essentiel pour HttpClient
  ]
};
```

### Créer le modèle TypeScript

#### core/models/task.model.ts
```typescript
export enum TaskStatus {
  TODO = 'TODO',
  IN_PROGRESS = 'IN_PROGRESS',
  DONE = 'DONE'
}

export interface Task {
  id: string;
  title: string;
  description: string;
  status: TaskStatus;
  createdAt: Date;
}

export interface CreateTaskRequest {
  title: string;
  description?: string;
  status: TaskStatus;
}
```

### Implémenter le Service HTTP

#### core/services/task.service.ts
```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Task, CreateTaskRequest } from '../models/task.model';

@Injectable({ providedIn: 'root' })
export class TaskService {
  private http = inject(HttpClient);
  private apiUrl = 'http://localhost:3000/tasks';

  getTasks(): Observable<Task[]> {
    return this.http.get<Task[]>(this.apiUrl);
  }

  getTask(id: string): Observable<Task> {
    return this.http.get<Task>(`${this.apiUrl}/${id}`);
  }

  createTask(task: CreateTaskRequest): Observable<Task> {
    return this.http.post<Task>(this.apiUrl, task);
  }

  updateTask(id: string, task: Partial<Task>): Observable<Task> {
    return this.http.patch<Task>(`${this.apiUrl}/${id}`, task);
  }

  deleteTask(id: string): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### Implémenter le Composant Liste

#### features/tasks/task-list/task-list.component.ts
```typescript
import { Component, OnInit, inject, signal } from '@angular/core';
import { CommonModule } from '@angular/common';
import { TaskService } from '../../../core/services/task.service';
import { Task } from '../../../core/models/task.model';
import { TaskItemComponent } from '../task-item/task-item.component';
import { TaskFormComponent } from '../task-form/task-form.component';

@Component({
  selector: 'app-task-list',
  standalone: true,
  imports: [CommonModule, TaskItemComponent, TaskFormComponent],
  template: `
    <div class="container">
      <h1>Mes Tâches</h1>
      
      <app-task-form (taskCreated)="onTaskCreated()"></app-task-form>

      @if (loading()) {
        <p>Chargement...</p>
      } @else if (error()) {
        <p class="error">{{ error() }}</p>
      } @else {
        <div class="tasks-list">
          @for (task of tasks(); track task.id) {
            <app-task-item 
              [task]="task"
              (taskDeleted)="onTaskDeleted($event)"
              (taskUpdated)="onTaskUpdated($event)">
            </app-task-item>
          } @empty {
            <p>Aucune tâche. Créez-en une !</p>
          }
        </div>
      }
    </div>
  `,
  styles: [`
    .container {
      max-width: 800px;
      margin: 0 auto;
      padding: 20px;
    }
    .tasks-list {
      margin-top: 20px;
    }
    .error {
      color: red;
    }
  `]
})
export class TaskListComponent implements OnInit {
  private taskService = inject(TaskService);

  tasks = signal<Task[]>([]);
  loading = signal(false);
  error = signal<string | null>(null);

  ngOnInit() {
    this.loadTasks();
  }

  loadTasks() {
    this.loading.set(true);
    this.error.set(null);

    this.taskService.getTasks().subscribe({
      next: (tasks) => {
        this.tasks.set(tasks);
        this.loading.set(false);
      },
      error: (err) => {
        this.error.set('Erreur lors du chargement des tâches');
        this.loading.set(false);
        console.error(err);
      }
    });
  }

  onTaskCreated() {
    this.loadTasks();
  }

  onTaskDeleted(taskId: string) {
    this.tasks.update(tasks => tasks.filter(t => t.id !== taskId));
  }

  onTaskUpdated(updatedTask: Task) {
    this.tasks.update(tasks => 
      tasks.map(t => t.id === updatedTask.id ? updatedTask : t)
    );
  }
}
```

### Implémenter le Composant Formulaire

#### features/tasks/task-form/task-form.component.ts
```typescript
import { Component, inject, output } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormBuilder, ReactiveFormsModule, Validators } from '@angular/forms';
import { TaskService } from '../../../core/services/task.service';
import { TaskStatus } from '../../../core/models/task.model';

@Component({
  selector: 'app-task-form',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  template: `
    <form [formGroup]="taskForm" (ngSubmit)="onSubmit()" class="task-form">
      <input 
        type="text" 
        formControlName="title" 
        placeholder="Titre de la tâche"
        [class.invalid]="taskForm.get('title')?.invalid && taskForm.get('title')?.touched">
      
      <input 
        type="text" 
        formControlName="description" 
        placeholder="Description (optionnel)">
      
      <select formControlName="status">
        <option value="TODO">À faire</option>
        <option value="IN_PROGRESS">En cours</option>
        <option value="DONE">Terminé</option>
      </select>
      
      <button type="submit" [disabled]="taskForm.invalid || submitting()">
        {{ submitting() ? 'Création...' : 'Créer la tâche' }}
      </button>

      @if (taskForm.get('title')?.invalid && taskForm.get('title')?.touched) {
        <p class="error">Le titre est requis</p>
      }
    </form>
  `,
  styles: [`
    .task-form {
      display: flex;
      flex-direction: column;
      gap: 10px;
      margin-bottom: 20px;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 5px;
    }
    input, select {
      padding: 10px;
      font-size: 16px;
    }
    input.invalid {
      border-color: red;
    }
    button {
      padding: 10px;
      background-color: #007bff;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:disabled {
      background-color: #ccc;
      cursor: not-allowed;
    }
    .error {
      color: red;
      margin: 0;
    }
  `]
})
export class TaskFormComponent {
  private fb = inject(FormBuilder);
  private taskService = inject(TaskService);

  taskCreated = output<void>();
  submitting = signal(false);

  taskForm = this.fb.group({
    title: ['', [Validators.required, Validators.minLength(3)]],
    description: [''],
    status: [TaskStatus.TODO, Validators.required]
  });

  onSubmit() {
    if (this.taskForm.valid) {
      this.submitting.set(true);

      const taskData = {
        title: this.taskForm.value.title!,
        description: this.taskForm.value.description,
        status: this.taskForm.value.status as TaskStatus
      };

      this.taskService.createTask(taskData).subscribe({
        next: () => {
          this.taskForm.reset({ status: TaskStatus.TODO });
          this.submitting.set(false);
          this.taskCreated.emit();
        },
        error: (err) => {
          console.error('Erreur création tâche:', err);
          this.submitting.set(false);
        }
      });
    }
  }
}
```

### 🔴 Erreurs Courantes Frontend

| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Oublier `provideHttpClient()` | HTTP ne marche pas | Ajouter dans `app.config.ts` |
| Ne pas typer les Observables | Pas d'autocomplétion | Toujours `Observable<Type>` |
| Subscribe sans unsubscribe | Memory leaks | Utiliser `async` pipe ou signals |
| Oublier `CommonModule` dans standalone component | Directives `*ngIf`/`*ngFor` ne marchent pas | Importer `CommonModule` |
| CORS errors | Requêtes bloquées | Vérifier `enableCors()` dans NestJS |

---

## Étape 3 : Gestion d'État avec Signals (Angular 20)

### 🖥️ Ce qui se passe
Les signals détectent automatiquement les changements et déclenchent le rendu uniquement des composants concernés (zone-less change detection).

### Créer un State Service

#### core/state/task-state.service.ts
```typescript
import { Injectable, signal, computed } from '@angular/core';
import { Task, TaskStatus } from '../models/task.model';

@Injectable({ providedIn: 'root' })
export class TaskStateService {
  private tasksSignal = signal<Task[]>([]);

  // Lectures computed (dérivées)
  tasks = this.tasksSignal.asReadonly();
  
  todoTasks = computed(() => 
    this.tasksSignal().filter(t => t.status === TaskStatus.TODO)
  );
  
  inProgressTasks = computed(() =>
    this.tasksSignal().filter(t => t.status === TaskStatus.IN_PROGRESS)
  );
  
  doneTasks = computed(() =>
    this.tasksSignal().filter(t => t.status === TaskStatus.DONE)
  );

  totalTasks = computed(() => this.tasksSignal().length);

  // Mutations
  setTasks(tasks: Task[]) {
    this.tasksSignal.set(tasks);
  }

  addTask(task: Task) {
    this.tasksSignal.update(tasks => [...tasks, task]);
  }

  updateTask(id: string, updates: Partial<Task>) {
    this.tasksSignal.update(tasks =>
      tasks.map(t => t.id === id ? { ...t, ...updates } : t)
    );
  }

  removeTask(id: string) {
    this.tasksSignal.update(tasks => tasks.filter(t => t.id !== id));
  }
}
```

### Utilisation dans un composant
```typescript
import { Component, inject, OnInit } from '@angular/core';
import { TaskStateService } from '../../../core/state/task-state.service';

@Component({
  template: `
    <div class="stats">
      <p>Total: {{ taskState.totalTasks() }}</p>
      <p>À faire: {{ taskState.todoTasks().length }}</p>
      <p>En cours: {{ taskState.inProgressTasks().length }}</p>
      <p>Terminé: {{ taskState.doneTasks().length }}</p>
    </div>
  `
})
export class TaskStatsComponent {
  taskState = inject(TaskStateService);
}
```

---

## Étape 4 : Authentification JWT

### Backend : Module Auth

#### auth/dto/login.dto.ts
```typescript
import { IsEmail, IsString, MinLength } from 'class-validator';

export class LoginDto {
  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  password: string;
}
```

#### auth/auth.service.ts
```typescript
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';

@Injectable()
export class AuthService {
  constructor(private jwtService: JwtService) {}

  async login(email: string, password: string) {
    // En production, vérifier dans la DB
    if (email === 'admin@example.com' && password === 'Password123') {
      const payload = { sub: '1', email };
      return {
        access_token: this.jwtService.sign(payload),
      };
    }
    throw new UnauthorizedException('Identifiants invalides');
  }
}
```

### Frontend : Intercepteur Auth

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

#### Enregistrer l'intercepteur dans app.config.ts
```typescript
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { authInterceptor } from './core/interceptors/auth.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor])),
    // ...
  ]
};
```

---

## ✅ Checklist Phase 5

Après cette phase, je suis capable de :
- [ ] Créer une API REST avec NestJS (GET, POST, PATCH, DELETE)
- [ ] Valider les données avec des DTOs et `class-validator`
- [ ] Gérer les erreurs avec des exceptions HTTP
- [ ] Créer une application Angular avec composants standalone
- [ ] Utiliser HttpClient pour communiquer avec une API
- [ ] Gérer l'état avec signals (Angular 20)
- [ ] Créer des formulaires réactifs avec validation
- [ ] Implémenter l'authentification JWT
- [ ] Utiliser des intercepteurs HTTP
- [ ] Structurer un projet full-stack professionnel

---

## 🎯 Mini-Projet Final : Application Complète

Ajouter les fonctionnalités suivantes :
1. **Recherche** : Filtrer les tâches par titre
2. **Tri** : Trier par date de création ou statut
3. **Pagination** : Afficher 10 tâches par page
4. **Drag & Drop** : Changer le statut en glissant-déposant
5. **Notifications** : Toast lors de création/suppression
6. **Tests** : Tests unitaires pour services et composants

---

## 📚 Ressources

- [Angular Official Docs](https://angular.dev)
- [NestJS Documentation](https://docs.nestjs.com)
- [Angular Signals Guide](https://angular.dev/guide/signals)
- [NestJS Course](https://www.youtube.com/watch?v=F_oOtaxb0L8)
- [Angular 20 Course](https://www.youtube.com/watch?v=3qBXWUpoPHo)
