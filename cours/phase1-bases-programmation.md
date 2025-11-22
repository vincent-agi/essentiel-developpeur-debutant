# Phase 1 — Bases de la Programmation

## 🎯 Objectif
Maîtriser la logique de programmation, les structures fondamentales, et savoir lire/corriger ses erreurs.

---

## 1. Variables et Types Primitifs

### 📚 Théorie Fondamentale
Une **variable** est un conteneur nommé qui stocke une valeur en mémoire. Elle possède :
- Un **nom** (identifiant)
- Un **type** (nature de la donnée)
- Une **valeur** (donnée stockée)

### 🖥️ Ce qui se passe dans l'ordinateur
1. **Déclaration** : Node.js/V8 réserve un emplacement en RAM (Random Access Memory)
2. **Assignation** : La valeur est écrite à cet emplacement mémoire
3. **Lecture** : Le CPU (processeur) récupère la valeur via son adresse mémoire
4. **Garbage Collection** : Node.js libère automatiquement la mémoire des variables non utilisées

```
RAM (Mémoire)
┌──────────────────┐
│ Adresse: 0x1A2B  │ ← Pointeur de la variable "prix"
│ Valeur: 19.99    │
└──────────────────┘
```

### 💻 Exemples TypeScript Typés

#### ❌ MAUVAIS (JavaScript sans typage)
```typescript
let prix = 19.99;              // Type implicite any en mode non-strict
let nom = "Laptop";
let enStock = true;
prix = "gratuit";              // Aucune erreur en JS pur → BUG futur
```

#### ✅ BON (TypeScript strict)
```typescript
let prix: number = 19.99;
const nom: string = "Laptop";
const enStock: boolean = true;
const tva: number = 0.20;
const prixTTC: number = prix * (1 + tva); // 23.988

// prix = "gratuit"; // ❌ Erreur TypeScript : Type 'string' is not assignable to type 'number'
```

### 🔴 Erreurs Courantes

| Erreur | Conséquence | Comment Éviter |
|--------|-------------|----------------|
| Utiliser `var` au lieu de `let/const` | Hoisting, pollution du scope global | **Règle** : Toujours `const` par défaut, `let` uniquement si réassignation nécessaire |
| Ne pas typer explicitement | Bugs runtime difficiles à tracer | Activer `strict: true` dans `tsconfig.json` |
| Réassigner une `const` | `TypeError: Assignment to constant variable` | Utiliser `let` si la variable doit changer |
| Noms de variables non descriptifs (`x`, `temp`, `data`) | Code illisible | Nommer selon le contexte métier : `userEmail`, `totalPrice` |

### 🎯 Cas d'Usage Concrets

#### Calcul de prix avec remise (E-commerce)
```typescript
const prixInitial: number = 99.99;
const codePromo: string = "NOEL2024";
const remisePourcent: number = 15;

const montantRemise: number = prixInitial * (remisePourcent / 100); // 14.9985
const prixFinal: number = prixInitial - montantRemise; // 85.0015
const prixAffiche: string = prixFinal.toFixed(2); // "85.00"

console.log(`Prix après remise ${codePromo} : ${prixAffiche}€`);
```

#### Gestion de statut utilisateur
```typescript
const estConnecte: boolean = true;
const aValideSonEmail: boolean = false;
const estAdmin: boolean = false;

const peutAccederAuDashboard: boolean = estConnecte && aValideSonEmail;
const peutModifierUtilisateurs: boolean = estConnecte && estAdmin;
```

---

## 2. Opérateurs

### 📚 Théorie Fondamentale
Les opérateurs permettent de manipuler et comparer des valeurs.

### 💻 Exemples TypeScript Typés

#### Arithmétiques
```typescript
const a: number = 10;
const b: number = 3;

console.log(a + b);  // 13 (addition)
console.log(a - b);  // 7 (soustraction)
console.log(a * b);  // 30 (multiplication)
console.log(a / b);  // 3.333... (division)
console.log(a % b);  // 1 (modulo/reste)
console.log(a ** b); // 1000 (puissance)
```

#### Comparaison
```typescript
const age: number = 25;

console.log(age === 25);  // true (égalité stricte)
console.log(age !== 30);  // true (différence stricte)
console.log(age > 18);    // true
console.log(age <= 25);   // true

// ❌ ÉVITER == et != (égalité faible, source de bugs)
console.log(5 == "5");    // true en JS (coercition de type)
console.log(5 === "5");   // false (typage strict)
```

#### Logiques
```typescript
const estMajeur: boolean = age >= 18;
const aPermis: boolean = true;
const peutConduire: boolean = estMajeur && aPermis; // AND

const estWeekend: boolean = true;
const estEnVacances: boolean = false;
const peutSeReposer: boolean = estWeekend || estEnVacances; // OR

const estOuvert: boolean = true;
const estFerme: boolean = !estOuvert; // NOT
```

### 🔴 Erreurs Courantes

| Erreur | Conséquence | Comment Éviter |
|--------|-------------|----------------|
| Utiliser `==` au lieu de `===` | Comparaisons imprévues (`0 == false` → `true`) | **Toujours utiliser `===` et `!==`** |
| Division par zéro | `Infinity` (pas d'erreur en JS) | Vérifier `if (diviseur !== 0)` avant de diviser |
| Confusion `=` (assignation) et `===` (comparaison) | `if (x = 5)` assigne au lieu de comparer | Linter détecte cette erreur si configuré |

---

## 3. Conditions

### 📚 Théorie Fondamentale
Les conditions permettent d'exécuter du code selon des critères.

### 🖥️ Ce qui se passe dans l'ordinateur
Le CPU évalue l'expression booléenne et saute (jump) à l'adresse mémoire du bloc de code correspondant.

### 💻 Exemples TypeScript Typés

#### if/else
```typescript
function obtenirMessagePrix(prix: number): string {
  if (prix === 0) {
    return "Gratuit";
  } else if (prix < 50) {
    return "Bon marché";
  } else if (prix < 200) {
    return "Prix moyen";
  } else {
    return "Premium";
  }
}

console.log(obtenirMessagePrix(0));    // "Gratuit"
console.log(obtenirMessagePrix(30));   // "Bon marché"
console.log(obtenirMessagePrix(150));  // "Prix moyen"
console.log(obtenirMessagePrix(500));  // "Premium"
```

#### switch
```typescript
type JourSemaine = "lundi" | "mardi" | "mercredi" | "jeudi" | "vendredi" | "samedi" | "dimanche";

function estJourTravail(jour: JourSemaine): boolean {
  switch (jour) {
    case "samedi":
    case "dimanche":
      return false;
    case "lundi":
    case "mardi":
    case "mercredi":
    case "jeudi":
    case "vendredi":
      return true;
    default:
      throw new Error(`Jour invalide: ${jour}`);
  }
}
```

#### Ternaire (condition compacte)
```typescript
const age: number = 17;
const statut: string = age >= 18 ? "Majeur" : "Mineur";

// Équivalent à :
// let statut: string;
// if (age >= 18) {
//   statut = "Majeur";
// } else {
//   statut = "Mineur";
// }
```

### 🔴 Erreurs Courantes

| Erreur | Conséquence | Comment Éviter |
|--------|-------------|----------------|
| Oublier `break` dans `switch` | Fall-through non intentionnel | ESLint avec `no-fallthrough` |
| Conditions trop imbriquées (`if` dans `if` dans `if`) | Code illisible | Utiliser early returns ou fonctions extraites |
| Comparaison avec `null`/`undefined` sans vérification | `TypeError: Cannot read property of null` | Utiliser `??` (nullish coalescing) ou `?.` (optional chaining) |

### 🎯 Cas d'Usage Concret : Calcul de Frais de Livraison

```typescript
interface Panier {
  montantTotal: number;
  estPremiumMembre: boolean;
  paysLivraison: string;
}

function calculerFraisLivraison(panier: Panier): number {
  // Early return pour livraison gratuite
  if (panier.estPremiumMembre) {
    return 0;
  }

  if (panier.montantTotal >= 100) {
    return 0; // Livraison gratuite au-dessus de 100€
  }

  // Frais selon le pays
  switch (panier.paysLivraison) {
    case "FR":
      return 5.99;
    case "BE":
    case "LU":
      return 7.99;
    case "DE":
    case "ES":
    case "IT":
      return 9.99;
    default:
      return 15.99; // Reste du monde
  }
}

// Tests
const panier1: Panier = { montantTotal: 150, estPremiumMembre: false, paysLivraison: "FR" };
console.log(calculerFraisLivraison(panier1)); // 0 (> 100€)

const panier2: Panier = { montantTotal: 50, estPremiumMembre: true, paysLivraison: "FR" };
console.log(calculerFraisLivraison(panier2)); // 0 (Premium)

const panier3: Panier = { montantTotal: 30, estPremiumMembre: false, paysLivraison: "DE" };
console.log(calculerFraisLivraison(panier3)); // 9.99
```

---

## 4. Boucles

### 📚 Théorie Fondamentale
Les boucles permettent de répéter du code plusieurs fois sans le dupliquer.

### 🖥️ Ce qui se passe dans l'ordinateur
Le CPU exécute les instructions en boucle jusqu'à ce que la condition soit fausse, puis saute hors de la boucle.

### 💻 Exemples TypeScript Typés

#### for classique
```typescript
// Afficher les nombres de 1 à 5
for (let i: number = 1; i <= 5; i++) {
  console.log(i);
}
```

#### for...of (itérer sur un tableau)
```typescript
const fruits: string[] = ["Pomme", "Banane", "Orange"];

for (const fruit of fruits) {
  console.log(fruit);
}
// Pomme
// Banane
// Orange
```

#### while
```typescript
let compteur: number = 0;
while (compteur < 3) {
  console.log(`Compteur: ${compteur}`);
  compteur++;
}
```

#### Méthodes Fonctionnelles (PRÉFÉRER)
```typescript
const nombres: number[] = [1, 2, 3, 4, 5];

// map : transformer chaque élément
const doubles: number[] = nombres.map(n => n * 2);
console.log(doubles); // [2, 4, 6, 8, 10]

// filter : garder seulement certains éléments
const pairs: number[] = nombres.filter(n => n % 2 === 0);
console.log(pairs); // [2, 4]

// reduce : agréger en une seule valeur
const somme: number = nombres.reduce((acc, n) => acc + n, 0);
console.log(somme); // 15
```

### 🔴 Erreurs Courantes

| Erreur | Conséquence | Comment Éviter |
|--------|-------------|----------------|
| Boucle infinie (`while(true)` sans `break`) | Application freeze, CPU 100% | Toujours avoir une condition de sortie |
| Modifier le tableau pendant l'itération | Comportement imprévisible | Créer une copie avec `[...array]` ou utiliser `filter/map` |
| Utiliser `for...in` sur un tableau | Itère sur les indices (string) + propriétés héritées | **Toujours `for...of` pour les tableaux** |
| Oublier le `return` dans `reduce` | `undefined` accumulé | Toujours retourner `acc` ou nouvelle valeur |

### 🎯 Cas d'Usage Concret : Calcul de Total Panier

```typescript
interface ProduitPanier {
  id: string;
  nom: string;
  prix: number;
  quantite: number;
}

const panier: ProduitPanier[] = [
  { id: "1", nom: "Laptop", prix: 999, quantite: 1 },
  { id: "2", nom: "Souris", prix: 25, quantite: 2 },
  { id: "3", nom: "Clavier", prix: 75, quantite: 1 }
];

// ❌ MAUVAIS : Boucle for classique
let total: number = 0;
for (let i = 0; i < panier.length; i++) {
  total += panier[i].prix * panier[i].quantite;
}

// ✅ BON : Méthode fonctionnelle (plus lisible)
const totalPanier: number = panier.reduce(
  (somme, produit) => somme + produit.prix * produit.quantite,
  0
);
console.log(totalPanier); // 1124

// Filtrer les produits > 50€
const produitsChers: ProduitPanier[] = panier.filter(p => p.prix > 50);
console.log(produitsChers); // [Laptop, Clavier]

// Obtenir seulement les noms
const noms: string[] = panier.map(p => p.nom);
console.log(noms); // ["Laptop", "Souris", "Clavier"]
```

---

## 5. Fonctions

### 📚 Théorie Fondamentale
Une fonction est un bloc de code réutilisable qui :
- Prend des **paramètres** (entrées)
- Effectue un **traitement**
- Retourne un **résultat** (sortie)

### 🖥️ Ce qui se passe dans l'ordinateur
1. **Appel de fonction** : Le CPU saute à l'adresse mémoire de la fonction
2. **Stack Frame** : Les paramètres et variables locales sont empilés en mémoire (call stack)
3. **Exécution** : Le code de la fonction s'exécute
4. **Retour** : La valeur de retour est placée dans un registre CPU, la stack frame est dépilée

### 💻 Exemples TypeScript Typés

#### Fonction basique
```typescript
function additionner(a: number, b: number): number {
  return a + b;
}

const resultat: number = additionner(5, 3); // 8
```

#### Fonction fléchée (arrow function)
```typescript
const multiplier = (a: number, b: number): number => a * b;

console.log(multiplier(4, 5)); // 20
```

#### Fonction pure vs impure
```typescript
// ✅ PURE : Même entrée → Même sortie, pas d'effet de bord
function calculerTVA(prixHT: number, tauxTVA: number): number {
  return prixHT * tauxTVA;
}

// ❌ IMPURE : Dépend d'une variable externe, a un effet de bord
let compteur = 0;
function incrementer(): number {
  compteur++; // Effet de bord
  return compteur;
}
```

#### Paramètres optionnels et valeurs par défaut
```typescript
function saluer(nom: string, titre?: string): string {
  if (titre) {
    return `Bonjour ${titre} ${nom}`;
  }
  return `Bonjour ${nom}`;
}

console.log(saluer("Dupont"));          // "Bonjour Dupont"
console.log(saluer("Dupont", "Dr."));   // "Bonjour Dr. Dupont"

// Avec valeur par défaut
function calculerRemise(prix: number, remise: number = 10): number {
  return prix * (1 - remise / 100);
}

console.log(calculerRemise(100));      // 90 (remise 10% par défaut)
console.log(calculerRemise(100, 20));  // 80 (remise 20%)
```

### 🔴 Erreurs Courantes

| Erreur | Conséquence | Comment Éviter |
|--------|-------------|----------------|
| Ne pas typer les paramètres et le retour | Perte des bénéfices de TypeScript | **Toujours typer** : `function f(x: number): string` |
| Oublier le `return` | Fonction retourne `undefined` | ESLint avec `@typescript-eslint/explicit-function-return-type` |
| Fonctions trop longues (> 20 lignes) | Difficile à tester et maintenir | Découper en plusieurs petites fonctions |
| Effets de bord non contrôlés | Bugs difficiles à tracer | Préférer les fonctions pures |

### 🎯 Cas d'Usage Concret : Validation de Formulaire

```typescript
interface Utilisateur {
  email: string;
  motDePasse: string;
  age: number;
}

// Fonctions de validation pures
function estEmailValide(email: string): boolean {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}

function estMotDePasseSecurise(mdp: string): boolean {
  return mdp.length >= 8 && /[A-Z]/.test(mdp) && /[0-9]/.test(mdp);
}

function estMajeur(age: number): boolean {
  return age >= 18;
}

// Fonction de validation globale
function validerInscription(user: Utilisateur): string[] {
  const erreurs: string[] = [];

  if (!estEmailValide(user.email)) {
    erreurs.push("Email invalide");
  }

  if (!estMotDePasseSecurise(user.motDePasse)) {
    erreurs.push("Mot de passe trop faible (min 8 caractères, 1 majuscule, 1 chiffre)");
  }

  if (!estMajeur(user.age)) {
    erreurs.push("Vous devez avoir 18 ans minimum");
  }

  return erreurs;
}

// Utilisation
const utilisateur: Utilisateur = {
  email: "john.doe@example.com",
  motDePasse: "Secure123",
  age: 25
};

const erreursValidation = validerInscription(utilisateur);
if (erreursValidation.length === 0) {
  console.log("✅ Inscription valide");
} else {
  console.log("❌ Erreurs :", erreursValidation);
}
```

---

## 6. Lecture de Messages d'Erreurs

### 📚 Méthode d'Analyse d'Erreur

#### 1. Lire l'erreur COMPLÈTEMENT (pas juste la première ligne)
```
Error: Cannot read property 'name' of undefined
    at getUserName (/app/user.ts:15:22)
    at processUsers (/app/main.ts:42:10)
    at Object.<anonymous> (/app/main.ts:50:1)
```

#### 2. Identifier le TYPE d'erreur
- **SyntaxError** : Erreur de syntaxe (oubli `;`, parenthèse, etc.)
- **TypeError** : Opération invalide sur un type (ex: accès à `undefined.property`)
- **ReferenceError** : Variable non définie
- **RangeError** : Valeur hors limites (ex: tableau[-1])

#### 3. Localiser la SOURCE
- **Fichier** : `/app/user.ts`
- **Ligne** : `15`
- **Colonne** : `22`

#### 4. Analyser la CAUSE
```typescript
// user.ts ligne 15
function getUserName(user) {
  return user.name; // ← Si user est undefined, crash ici
}
```

#### 5. CORRIGER
```typescript
function getUserName(user?: { name: string }): string {
  return user?.name ?? "Anonyme"; // ✅ Gestion du cas undefined
}
```

### 🎯 Cas d'Usage Concret : Debugging Étape par Étape

#### Erreur rencontrée
```
TypeError: Cannot read properties of undefined (reading 'length')
    at filterProducts (/app/products.ts:10:30)
```

#### Code problématique
```typescript
function filterProducts(products, category) {
  return products.filter(p => p.category === category);
}

const result = filterProducts(undefined, "Electronics"); // CRASH
```

#### Solution avec TypeScript et gestion d'erreur
```typescript
interface Product {
  id: string;
  name: string;
  category: string;
}

function filterProducts(
  products: Product[] | undefined,
  category: string
): Product[] {
  if (!products) {
    console.warn("filterProducts: products is undefined, returning empty array");
    return [];
  }

  return products.filter(p => p.category === category);
}

// Utilisation sécurisée
const products: Product[] | undefined = fetchProducts(); // Peut retourner undefined
const electronics = filterProducts(products, "Electronics"); // ✅ Pas de crash
```

### 🔴 Erreurs Courantes de Débutants

| Erreur | Message | Cause | Solution |
|--------|---------|-------|----------|
| Accès à propriété de `undefined` | `Cannot read property 'x' of undefined` | Variable non initialisée ou retour null | Vérifier `if (obj)` ou utiliser `obj?.x` |
| Variable non définie | `ReferenceError: x is not defined` | Typo ou variable non déclarée | Vérifier l'orthographe, déclarer avec `const/let` |
| Parenthèse oubliée | `SyntaxError: Unexpected token` | Oubli `)` ou `}` | Compter les parenthèses ouvrantes/fermantes |
| Mauvais type | `Argument of type 'string' is not assignable to parameter of type 'number'` | Passage d'un string à une fonction attendant un number | Convertir avec `Number()` ou corriger le type |

---

## 🎯 Mini-Projet Guidé : Calculatrice CLI

### Objectif
Créer une calculatrice en ligne de commande TypeScript avec gestion d'erreurs.

### Étapes

1. **Setup**
```bash
mkdir calculatrice && cd calculatrice
npm init -y
npm install -D typescript @types/node
npx tsc --init
```

2. **Code**
```typescript
// calculatrice.ts
enum Operation {
  ADDITION = "+",
  SOUSTRACTION = "-",
  MULTIPLICATION = "*",
  DIVISION = "/"
}

function calculer(a: number, b: number, operation: Operation): number {
  switch (operation) {
    case Operation.ADDITION:
      return a + b;
    case Operation.SOUSTRACTION:
      return a - b;
    case Operation.MULTIPLICATION:
      return a * b;
    case Operation.DIVISION:
      if (b === 0) {
        throw new Error("Division par zéro impossible");
      }
      return a / b;
    default:
      throw new Error(`Opération inconnue: ${operation}`);
  }
}

// Lecture des arguments CLI
const args = process.argv.slice(2);

if (args.length !== 3) {
  console.error("Usage: node calculatrice.js <nombre1> <opération> <nombre2>");
  console.error("Exemple: node calculatrice.js 10 + 5");
  process.exit(1);
}

const nombre1 = Number(args[0]);
const operation = args[1] as Operation;
const nombre2 = Number(args[2]);

if (isNaN(nombre1) || isNaN(nombre2)) {
  console.error("Erreur: Les nombres doivent être valides");
  process.exit(1);
}

try {
  const resultat = calculer(nombre1, nombre2, operation);
  console.log(`${nombre1} ${operation} ${nombre2} = ${resultat}`);
} catch (error) {
  if (error instanceof Error) {
    console.error(`Erreur: ${error.message}`);
  }
  process.exit(1);
}
```

3. **Compilation et exécution**
```bash
npx tsc calculatrice.ts
node calculatrice.js 15 + 7
# 15 + 7 = 22

node calculatrice.js 20 / 4
# 20 / 4 = 5

node calculatrice.js 10 / 0
# Erreur: Division par zéro impossible
```

---

## ✅ Checklist d'Auto-Évaluation Phase 1

Avant de passer à la Phase 2, je sais :
- [ ] Déclarer des variables avec `const` et `let` (jamais `var`)
- [ ] Typer explicitement mes variables en TypeScript
- [ ] Utiliser les opérateurs de comparaison stricte (`===`, `!==`)
- [ ] Écrire des conditions avec `if/else` et `switch`
- [ ] Préférer les méthodes fonctionnelles (`map/filter/reduce`) aux boucles `for`
- [ ] Écrire des fonctions typées avec paramètres et retour explicites
- [ ] Lire et comprendre un message d'erreur TypeScript
- [ ] Déboguer avec `console.log()` et identifier la ligne problématique
- [ ] Éviter les erreurs courantes (division par zéro, accès à `undefined`, etc.)

**Test Pratique** : Créer un convertisseur d'unités (km ↔ miles, °C ↔ °F) avec validation d'entrée et gestion d'erreurs.

---

## 📚 Ressources Complémentaires

- [JavaScript Basics](https://www.youtube.com/watch?v=W6NZfCO5SIk)
- [Array Methods](https://www.youtube.com/watch?v=s9wW2PpJsmQ)
- [Functions Deep Dive](https://www.youtube.com/watch?v=PkZNo7MFNFg)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
