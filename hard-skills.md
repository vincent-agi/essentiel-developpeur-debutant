# Parcours Hard Skills — Développeur Full Stack & Conception Logicielle

> 🎯 **Objectif** : Devenir opérationnel en **8 semaines** sur la stack **Angular 20 + NestJS avec TypeScript**
> 
> 📖 **Voir aussi** : [Bilan et Propositions d'Améliorations](./BILAN.md)

**Durée totale** : 8 semaines pour être opérationnel en entreprise

---

## Phase 0 — Préparer le terrain (1 jour)
- [ ] Comprendre ce qu'est un programme
- [ ] Installer et utiliser correctement l'environnement de développement

### 📚 Notions
- [ ] Programme : code → compilation/transpilation → exécution
- [ ] **Ce qui se passe dans l'ordinateur** : CPU, RAM, stockage
- [ ] Node.js + NPM (gestionnaire de paquets)
- [ ] VSCode : debugging, extensions essentielles (ESLint, Prettier, TypeScript)

### 🎯 Compétences Acquises
À la fin de cette phase, vous savez :
- [ ] Installer Node.js et vérifier la version (`node --version`)
- [ ] Créer un projet avec `npm init`
- [ ] Installer des packages avec `npm install`
- [ ] Utiliser le debugger de VSCode

### 📚 Ressources
- [ ] https://www.youtube.com/watch?v=ENrzD9HAZK4 (Node.js expliqué)
- [ ] https://www.youtube.com/watch?v=VqCgcpAypFQ (VSCode)

---

## Phase 1 — Bases de la Programmation (1 semaine)
**Objectif** : Maîtriser la logique, les structures fondamentales, les fonctions et savoir déboguer.

> 📖 **Cours détaillé** : [Phase 1 - Bases de la Programmation](./cours/phase1-bases-programmation.md)

### 📚 Notions (dans l'ordre)
Chaque notion inclut :
- ✅ Théorie fondamentale
- ✅ Ce qui se passe dans l'ordinateur (CPU, mémoire)
- ✅ Exemples TypeScript typés
- ✅ Erreurs courantes + comment les éviter
- ✅ Cas d'usage concrets métier

1. **Variables et types primitifs**
   - `const` vs `let` (jamais `var`)
   - Types : `number`, `string`, `boolean`
   - Typage explicite en TypeScript
   
2. **Opérateurs**
   - Arithmétiques : `+`, `-`, `*`, `/`, `%`, `**`
   - Comparaison stricte : `===`, `!==`, `>`, `<`, `>=`, `<=`
   - Logiques : `&&`, `||`, `!`
   
3. **Conditions**
   - `if/else`, `switch`, ternaire
   - Early returns
   - Cas d'usage : calcul de frais de livraison
   
4. **Boucles**
   - `for`, `while`, `for...of`
   - **Préférer** : `.map()`, `.filter()`, `.reduce()`
   - Cas d'usage : calcul total panier
   
5. **Fonctions**
   - Paramètres typés et valeur de retour
   - Fonctions pures vs impures
   - Cas d'usage : validation de formulaire
   
6. **Lecture de messages d'erreurs**
   - Méthode d'analyse étape par étape
   - SyntaxError, TypeError, ReferenceError
   - Debugging avec `console.log()` et debugger

### 🎯 Mini-Projets
- [ ] Calculatrice CLI (opérations basiques + gestion d'erreurs)
- [ ] Convertisseur d'unités (km ↔ miles, °C ↔ °F)
- [ ] Générateur de menus aléatoires

### ✅ Checklist d'Auto-Évaluation
Avant de passer à la Phase 2, je sais :
- [ ] Déclarer des variables avec `const` et `let` typées
- [ ] Utiliser les opérateurs de comparaison stricte (`===`)
- [ ] Écrire des conditions et préférer early returns
- [ ] Utiliser `map/filter/reduce` au lieu de boucles `for`
- [ ] Écrire des fonctions typées
- [ ] Lire et corriger un message d'erreur TypeScript

### 📚 Ressources
- [ ] https://www.youtube.com/watch?v=W6NZfCO5SIk (bases JS)
- [ ] https://www.youtube.com/watch?v=s9wW2PpJsmQ (boucles)
- [ ] https://www.youtube.com/watch?v=PkZNo7MFNFg&t=330s (fonctions)
- [ ] https://www.typescriptlang.org/docs/handbook/intro.html (TypeScript Handbook)

---

## Phase 2 — TypeScript Propre (1 semaine)
**Objectif** : Typage strict + structuration modulaire pour éviter les bugs runtime.

### 📚 Notions
- [ ] Types primitifs vs composés
- [ ] `type` vs `interface` (quand utiliser quoi)
- [ ] Objets + immutabilité (spread `...`, référence vs valeur)
- [ ] Tableaux + méthodes fonctionnelles (`map`, `filter`, `reduce`)
- [ ] `enum` / `const enum`
- [ ] Fonctions → signatures typées
- [ ] Gestion des erreurs (`try/catch`, erreurs custom)
- [ ] Imports/Exports organisés
- [ ] **Optional Chaining** (`?.`) et **Nullish Coalescing** (`??`)

### 🔴 Erreurs Courantes et Solutions
| Erreur | Conséquence | Solution |
|--------|-------------|----------|
| Utiliser `any` | Perd les avantages de TypeScript | Activer `strict: true` dans `tsconfig.json` |
| Muter des objets directement | Bugs difficiles à tracer | Utiliser spread `{ ...obj, newProp }` |
| Ne pas gérer `null`/`undefined` | `TypeError` runtime | Utiliser `?.` et `??` |

### 🎯 Cas d'Usage Concrets
| Cas | Code TypeScript | Contexte |
|-----|-----------------|----------|
| Filtrer produits par catégorie | `products.filter(p => p.category === 'Electronics')` | E-commerce |
| Calculer total panier | `items.reduce((sum, i) => sum + i.price, 0)` | Checkout |
| Transformer données API | `users.map(u => ({ id: u.id, name: u.fullName }))` | Normalisation |

### 🎯 Mini-Projet
- [ ] Todolist typée + refactor fonctionnel (`map/filter/reduce`)

### ✅ Checklist d'Auto-Évaluation
- [ ] Typer explicitement toutes mes variables
- [ ] Utiliser `interface` pour les objets métier
- [ ] Éviter `any` (strict mode activé)
- [ ] Utiliser `map/filter/reduce` au lieu de boucles
- [ ] Organiser mon code en modules

### 📚 Ressources
- [ ] https://www.youtube.com/watch?v=30LWjhZzg50 (TS course)
- [ ] https://www.youtube.com/c/mattpocockuk/videos (TS clean practices)

---

## Phase 3 — Clean Code + OOP + SOLID (1 semaine)
**Objectif** : Écrire du code **maintenable, lisible, modulaire** dès le départ.

### 📚 Notions Clean Code
- [ ] Nommage clair (pas de `x`, `temp`, `data`)
- [ ] Fonctions courtes (< 20 lignes) + une seule responsabilité
- [ ] DRY (Don't Repeat Yourself - pas de duplication)
- [ ] KISS (Keep It Simple, Stupid - simplicité)
- [ ] YAGNI (You Aren't Gonna Need It - ne pas sur-concevoir)

### 📚 Notions OOP
- [ ] Classes, objets, méthodes
- [ ] Encapsulation (private, public, protected)
- [ ] **Composition > Héritage** (préférer la composition)
- [ ] Interfaces vs classes abstraites

### 📚 SOLID (Principes de conception)
- [ ] **S** : Single Responsibility (une classe = une responsabilité)
- [ ] **O** : Open/Closed (ouvert à l'extension, fermé à la modification)
- [ ] **L** : Liskov Substitution (sous-types interchangeables)
- [ ] **I** : Interface Segregation (interfaces spécifiques)
- [ ] **D** : Dependency Inversion (dépendre d'abstractions, pas de concrétions)

### 🎯 Exemple Avant/Après

#### ❌ MAUVAIS
```typescript
function processOrder(order: any) {
  let total = 0;
  for (let item of order.items) {
    total += item.price * item.quantity;
  }
  if (order.coupon) total *= 0.9;
  // envoyer email, mettre à jour stock, créer facture...
  return total;
}
```

#### ✅ BON
```typescript
function calculateOrderTotal(items: OrderItem[]): number {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}

function applyDiscount(total: number, coupon?: Coupon): number {
  return coupon ? total * (1 - coupon.discount) : total;
}

function processOrder(order: Order): number {
  validateOrder(order);
  const subtotal = calculateOrderTotal(order.items);
  const total = applyDiscount(subtotal, order.coupon);
  sendConfirmationEmail(order);
  updateStock(order.items);
  generateInvoice(order, total);
  return total;
}
```

### 🎯 Mini-Projets
- [ ] Panier e-commerce (OOP + responsabilité)
- [ ] Service de notifications UI (Pattern Builder)

### ✅ Checklist d'Auto-Évaluation
- [ ] Mes fonctions font < 20 lignes
- [ ] Mes classes ont une seule responsabilité
- [ ] J'utilise la composition plutôt que l'héritage
- [ ] Mon code est lisible sans commentaires

### 📚 Ressources
- [ ] https://www.youtube.com/watch?v=pTB30aXS77U (SOLID)
- [ ] https://www.youtube.com/watch?v=7EmboKQH8lM (Clean Code concret)

---

## Phase 4 — Design Patterns (1 semaine)
**Objectif** : Éviter de réinventer la roue et structurer le code avec des patterns éprouvés.

### �� Patterns Prioritaires (avec usages concrets web)
| Pattern | Usage Typique | Exemple Concret |
|---------|---------------|-----------------|
| **Builder** | Configurer snackbars / modals / formulaires | `new ModalBuilder().title('Confirmer').okButton().build()` |
| **Factory** | Créer services API selon environnement | `ApiServiceFactory.create(env)` |
| **Strategy** | Remplacer `if/else` multiples | Calcul TVA selon pays |
| **Adapter** | Uniformiser API externes | Adapter Stripe/PayPal en `PaymentProvider` |
| **Observer** | Streaming d'événements UI | RxJS Observables |
| **DTO** | Contrats Front ↔ Back | Validation avec `class-validator` |

### 🎯 Exemple Concret : Strategy Pattern
```typescript
interface TaxStrategy {
  calculate(price: number): number;
}

class FrenchTax implements TaxStrategy {
  calculate(price: number): number {
    return price * 1.20; // TVA 20%
  }
}

class GermanTax implements TaxStrategy {
  calculate(price: number): number {
    return price * 1.19; // TVA 19%
  }
}

class PriceCalculator {
  constructor(private taxStrategy: TaxStrategy) {}

  calculateFinalPrice(price: number): number {
    return this.taxStrategy.calculate(price);
  }
}

// Utilisation
const frenchCalc = new PriceCalculator(new FrenchTax());
console.log(frenchCalc.calculateFinalPrice(100)); // 120

const germanCalc = new PriceCalculator(new GermanTax());
console.log(germanCalc.calculateFinalPrice(100)); // 119
```

### ✅ Checklist d'Auto-Évaluation
- [ ] Je reconnais quand utiliser un Builder
- [ ] Je sais créer une Factory simple
- [ ] Je remplace les `if/else` multiples par Strategy
- [ ] Je comprends le pattern Observer (RxJS)

### 📚 Ressource
- [ ] https://refactoring.guru/fr/design-patterns

---

## Phase 5 — Architecture Web : Angular 20 + NestJS (3 semaines)
**Objectif** : Full-stack opérationnel avec projet fil rouge (Application CRUD complète).

> 📖 **Cours détaillé** : [Phase 5 - Angular + NestJS Opérationnel](./cours/phase5-angular-nestjs-operationnel.md)

### 🎯 Projet Fil Rouge : Application de Gestion de Tâches
Construire une application complète en 10 étapes guidées :
1. Setup Backend NestJS
2. Créer DTOs avec validation
3. Implémenter CRUD (Create, Read, Update, Delete)
4. Setup Frontend Angular 20
5. Service HTTP typé
6. Composants avec Signals
7. Formulaires réactifs
8. Authentification JWT
9. Guards et Intercepteurs
10. Déploiement

### 📚 Angular 20
- [ ] Composants standalone
- [ ] Signals (state management moderne)
- [ ] Dependency Injection
- [ ] HttpClient typé
- [ ] Reactive Forms avec validation
- [ ] RxJS (progressif : `map` → `switchMap` → `debounceTime`)
- [ ] Guards (protection de routes)
- [ ] Intercepteurs HTTP

### 📚 NestJS
- [ ] Modules, Controllers, Services
- [ ] DTO + Validation (`class-validator`)
- [ ] Exception Filters
- [ ] Auth JWT avec Passport
- [ ] Guards & Interceptors
- [ ] CORS configuration

### 🔴 Erreurs Courantes Backend
| Erreur | Solution |
|--------|----------|
| Oublier `class-validator` | `npm i class-validator class-transformer` |
| Ne pas activer `ValidationPipe` | Ajouter dans `main.ts` |
| CORS non configuré | `app.enableCors()` |

### 🔴 Erreurs Courantes Frontend
| Erreur | Solution |
|--------|----------|
| Oublier `provideHttpClient()` | Ajouter dans `app.config.ts` |
| Ne pas typer les Observables | `Observable<Type>` |
| Subscribe sans unsubscribe | Utiliser `async` pipe ou signals |

### ✅ Checklist d'Auto-Évaluation
- [ ] Créer une API REST avec NestJS
- [ ] Valider les données avec DTOs
- [ ] Créer une application Angular avec composants standalone
- [ ] Utiliser Signals pour le state management
- [ ] Implémenter l'authentification JWT
- [ ] Protéger des routes avec Guards

### 📚 Ressources
- [ ] Angular : https://www.youtube.com/watch?v=3qBXWUpoPHo
- [ ] NestJS : https://www.youtube.com/watch?v=F_oOtaxb0L8
- [ ] Angular Docs : https://angular.dev
- [ ] NestJS Docs : https://docs.nestjs.com

---

## Phase 6 — Essentiels Pro (Junior Opérationnel) (1 semaine)
**Objectif** : Maîtriser les concepts qu'un junior rencontre **dès les premiers mois en entreprise**.

> ⚠️ **Phase simplifiée** : Enterprise Integration Patterns (Kafka, RabbitMQ) retirés pour se concentrer sur l'opérationnalité immédiate.
> 
> 📖 **Cours détaillé** : [Phase 6 - Essentiels Pro](./cours/phase6-essentiels-pro.md)

### 📚 Notions Essentielles
1. **State Management Avancé**
   - Signals (Angular 20)
   - State centralisé avec services
   - Computed values dérivés
   
2. **Authentification JWT (Production-Ready)**
   - Backend : JwtStrategy, Guards
   - Frontend : Intercepteurs, Guards
   - Gestion du refresh token
   
3. **Gestion d'Erreurs Centralisée**
   - Error Interceptor (Frontend)
   - Exception Filters (Backend)
   - Notifications utilisateur (toasts)
   
4. **Tests (Essentiel Junior)**
   - Tests unitaires NestJS (Jest)
   - Tests composants Angular (Jasmine/Karma)
   - Couverture de code > 70%
   
5. **Déploiement Basique**
   - Backend : Render.com / Railway
   - Frontend : Netlify / Vercel
   - Variables d'environnement

### 🔴 Erreurs Courantes
| Erreur | Solution |
|--------|----------|
| Muter directement un signal | Toujours `.update()` ou `.set()` |
| Stocker JWT en clair | httpOnly cookies + SameSite |
| Ne pas tester son code | Écrire tests unitaires (> 70% couverture) |

### ✅ Checklist d'Auto-Évaluation
- [ ] Gérer un state global avec signals
- [ ] Implémenter l'authentification JWT complète
- [ ] Gérer les erreurs de manière centralisée
- [ ] Écrire des tests unitaires
- [ ] Déployer une application en production

### 📚 Ressources
- [ ] Angular Signals : https://angular.dev/guide/signals
- [ ] Auth JWT : https://www.youtube.com/watch?v=uAKzFhE3rxU
- [ ] Tests Angular : https://www.youtube.com/watch?v=BumgayeUC08
- [ ] Tests NestJS : https://docs.nestjs.com/fundamentals/testing

---

## 🎯 Projet Final : Portfolio Production-Ready

À la fin du parcours, créer une application complète de gestion de projets avec :
- ✅ Authentification JWT
- ✅ CRUD complet (Projets, Tâches, Utilisateurs)
- ✅ State management avec Signals
- ✅ Gestion d'erreurs centralisée
- ✅ Tests unitaires (couverture > 70%)
- ✅ Déployée en production (Netlify + Render)
- ✅ Repository GitHub avec README professionnel

---

## 📊 Temps Total pour Être Opérationnel : 8 Semaines

| Phase | Durée | Objectif |
|-------|-------|----------|
| Phase 0 | 1 jour | Setup environnement |
| Phase 1 | 1 semaine | Bases programmation |
| Phase 2 | 1 semaine | TypeScript strict |
| Phase 3 | 1 semaine | Clean Code + SOLID |
| Phase 4 | 1 semaine | Design Patterns |
| Phase 5 | 3 semaines | Angular + NestJS |
| Phase 6 | 1 semaine | Essentiels Pro |

**Total** : 8 semaines pour être **opérationnel en entreprise** avec un **portfolio GitHub** de qualité.

---

## 📖 Documents Complémentaires

- [Bilan et Propositions d'Améliorations](./BILAN.md) - Analyse complète du cours
- [Savoir-être Professionnel](./soft-skills.md) - Compétences comportementales
