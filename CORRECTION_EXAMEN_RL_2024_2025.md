# CORRECTION COMPLÈTE — Examen RL EFREI 2024/2025

**Durée** : 1h50 │ **Total** : 40 pts (+4 pts bonus) │ **Professeur** : Victor Morand

---

# 1. Généralités

## 1.1 Quelques définitions

---

### Question 1 (3 pts) — Schématisez le cadre général du RL. Nommez et définissez les variables.

Schéma général du RL

**Le schéma** représente une boucle d'interaction continue entre deux entités :

```
              Action A(t)
   ┌───────┐ ──────────────► ┌──────────────────┐
   │ AGENT │                 │  ENVIRONNEMENT   │
   └───────┘ ◄────────────── └──────────────────┘
               État S(t+1)
               Récompense R(t+1)
```

**La boucle à chaque pas de temps t :**

1. L'agent observe l'état S(t) et reçoit la récompense R(t)
2. L'agent choisit et exécute une action A(t)
3. L'environnement réagit : il produit un nouvel état S(t+1) et une récompense R(t+1)
4. t ← t+1, on recommence

**Les 5 variables :**


| Variable            | Définition                                                                                              |
| ------------------- | ------------------------------------------------------------------------------------------------------- |
| **Agent**           | Le système (le "cerveau") qui prend des décisions séquentielles pour maximiser ses récompenses cumulées |
| **Environnement**   | Le système externe avec lequel l'agent interagit ; il produit les états et les récompenses              |
| **État S(t)**       | Description complète de la situation actuelle, observée depuis l'environnement par l'agent              |
| **Action A(t)**     | La décision ou le mouvement effectué par l'agent dans un état donné                                     |
| **Récompense R(t)** | Signal numérique de feedback évaluant la qualité de l'action prise sous un certain état                 |


> 🐕 **Pour mieux comprendre** : C'est comme dresser un chien. Le chien (agent) voit une situation (état), fait quelque chose (action), et reçoit un biscuit ou un "non !" (récompense). À force d'essais, il apprend à maximiser les biscuits.

---

### Question 2 (1 pt) — Qu'est-ce qu'un MDP et comment le définit-on ?

Un **MDP (Markov Decision Process)** est le cadre mathématique formel du RL.

**Définition** : C'est un tuple **(S, A, P, R, γ)** où :

- **S** : ensemble fini d'états
- **A** : ensemble fini d'actions
- **P** : probabilités de transition → p(s'|s, a) = probabilité d'aller en s' depuis s en prenant a
- **R** : fonction dtu e récompense → p(r|s, a) = distribution de la récompense obtenue
- **γ** : facteur d'actualisation (discount factor)

Il vérifie la **propriété de Markov** : l'état futur ne dépend **que** de l'état présent et de l'action, **pas** de l'historique.

> 🎲 **Pour mieux comprendre** : Aux échecs, pour décider du prochain coup, il suffit de regarder le plateau actuel. Peu importe comment on en est arrivé là — c'est la propriété de Markov.

---

### Question 3a (1 pt) — Value Function V

**Définition** :

> V^π(s) = E[G(t) | S(t) = s]

C'est l'**espérance du gain cumulé** (somme des récompenses futures pondérées) en partant de l'état s et en suivant la politique π.

> 🧭 **Pour mieux comprendre** : V(s), c'est la réponse à "à quel point c'est bien d'être dans cet état ?". Dans un labyrinthe, une case près de la sortie a un V élevé, une case dans un cul-de-sac a un V faible.

---

### Question 3b (1 pt) — Action-Value Function Q

**Définition** :

> Q^π(s, a) = E[G(t) | S(t) = s, A(t) = a]

C'est l'**espérance du gain cumulé** en partant de l'état s, en prenant l'action a, puis en suivant la politique π.

> 🧭 **Pour mieux comprendre** : Q(s, a), c'est la réponse à "à quel point c'est bien d'être dans cet état ET de prendre cette action précise ?". V te dit "cette case est bien", Q te dit "cette case est bien SI tu vas à droite" (mais peut-être pas si tu vas à gauche).

**Lien V ↔ Q** : V^π(s) = Σ_a π(a|s) · Q^π(s, a)  →  V est la moyenne de Q sur toutes les actions possibles.

---

### Question 3c (1 pt) — Gain G

**Définition** :

> G(t) = r(t) + γ·r(t+1) + γ²·r(t+2) + ... = Σ(k=0→∞) γᵏ · r(t+k)

C'est la **somme pondérée (actualisée) des récompenses futures** à partir du temps t. Le facteur γ fait que les récompenses lointaines comptent moins.

> 💰 **Pour mieux comprendre** : C'est comme le total de tes gains au casino. Mais les gains de demain valent un peu moins que les gains d'aujourd'hui (à cause de γ). Si γ = 0.9, 100€ demain = 90€ d'aujourd'hui.

---

### Question 4 (1 pt) — Discount Factor γ, c'est quoi et à quoi ça sert ?

Le **discount factor** γ ∈ [0, 1] pondère l'importance relative des récompenses futures par rapport aux récompenses immédiates.

**À quoi sert-il :**

1. **Contrôle la vision temporelle** : γ = 0 → agent myope (ne regarde que le présent). γ → 1 → agent prévoyant (valorise autant le futur).
2. **Garantit la convergence mathématique** : quand il n'y a pas d'état terminal, γ < 1 empêche le gain G de diverger vers l'infini.

> 💸 **Pour mieux comprendre** : Préfères-tu 100€ maintenant ou 100€ dans 1 an ? Évidemment maintenant. γ encode exactement cette préférence. Avec γ = 0.9, 100€ dans un an = 90€ aujourd'hui. Plus γ est petit, plus l'agent est impatient.

---

### Question 5 (1 pt) — Comment définir une récompense efficace ? Exemple concret.

**Principes d'une bonne récompense :**

- Récompenses **positives** pour le comportement souhaité
- Récompenses **négatives** (pénalités) pour les comportements indésirables
- Doit être **fréquente et informative** (pas uniquement à la fin), sinon l'agent ne sait pas s'il progresse (problème de récompense sparse)

**Exemple concret — Hélicoptère en vol acrobatique** (exemple du cours slide 10) :

- **+récompense** pour suivre la trajectoire désirée
- **-récompense** pour un crash
- On donne du feedback à chaque pas de temps, pas uniquement à l'atterrissage

> ⚠️ **Piège** : Si on ne donne +1 qu'à l'arrivée d'un labyrinthe et 0 sinon, l'agent met très longtemps à apprendre car il ne sait pas s'il progresse. Mieux vaut donner -1 à chaque pas (il apprend à finir vite) ou un petit bonus quand il se rapproche de la sortie.

---

## 1.2 Concepts Généraux

---

### Question 6 (1 pt) — Model-Free vs Model-Based


| Model-Free                                                   | Model-Based                                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| Apprend **directement** par interaction avec l'environnement | Utilise ou apprend un **modèle** de l'environnement (transitions P et récompenses R) |
| N'a **pas besoin** de connaître P ni R                       | A besoin de P et R (connus ou appris)                                                |
| Ex: Q-Learning, SARSA, PPO                                   | Ex: Value Iteration, AlphaZero, MuZero                                               |


> 🍳 **Pour mieux comprendre** : Model-Free, c'est apprendre à cuisiner par essai-erreur (tu goûtes, tu corriges). Model-Based, c'est suivre un livre de recettes (tu sais que 200g de farine + 3 oeufs = crêpe avant même de commencer).

---

### Question 7 (1 pt) — Policy-based vs Value-based


| Value-Based                                               | Policy-Based                                |
| --------------------------------------------------------- | ------------------------------------------- |
| Apprend une **fonction de valeur** V(s) ou Q(s,a)         | Apprend **directement** la politique π(a|s) |
| La politique est **déduite** de la valeur (action greedy) | Pas de fonction de valeur explicite         |
| Fonctionne bien en **discret**                            | Fonctionne aussi en **continu**             |
| Ex: Q-Learning, DQN                                       | Ex: REINFORCE, TRPO                         |


**Actor-Critic** combine les deux : un actor (politique) + un critic (valeur). Ex: PPO, A2C, SAC.

> 🎮 **Pour mieux comprendre** : Value-based = tu notes chaque case de la carte sur 10, et tu vas toujours vers la meilleure note. Policy-based = tu apprends directement "dans cette situation, fais ça" sans noter chaque case.

---

### Question 8 (1 pt) — Off-Policy vs On-Policy


| On-Policy                                        | Off-Policy                                                                      |
| ------------------------------------------------ | ------------------------------------------------------------------------------- |
| Apprend la valeur de **la politique qu'il suit** | Apprend la valeur d'**une autre politique** (typiquement la politique optimale) |
| Politique cible = politique de comportement      | Politique cible ≠ politique de comportement                                     |
| Les données doivent être "fraîches"              | Peut **réutiliser** d'anciennes données (via replay buffer)                     |
| Ex: SARSA, A2C, PPO                              | Ex: Q-Learning, DQN, SAC, TD3                                                   |


**Astuce pour se souvenir** :

- **SARSA** utilise Q(s', **a'**) → a' est l'action réellement prise → **on-policy**
- **Q-Learning** utilise **max_a'** Q(s', a') → prend le meilleur théorique → **off-policy**

> 📚 **Pour mieux comprendre** : On-policy = tu n'apprends que de tes propres copies corrigées. Off-policy = tu lis aussi les copies des autres (même d'anciens élèves) pour progresser.

---

### Question 9 (1 pt) — TD vs Monte Carlo


| Temporal Difference (TD)                                     | Monte Carlo (MC)                              |
| ------------------------------------------------------------ | --------------------------------------------- |
| Met à jour à **chaque pas de temps**                         | Met à jour en **fin d'épisode** seulement     |
| Utilise le **bootstrapping** (estime à partir d'estimations) | Utilise le **retour complet réel** G(t)       |
| **Biaisé** mais **faible variance**                          | **Non biaisé** mais **haute variance**        |
| Converge généralement **plus vite**                          | Nécessite des **épisodes terminaux complets** |


**Formules clés** :

- **MC** : V(s) ← V(s) + α·[**G(t)** - V(s)]
- **TD(0)** : V(s) ← V(s) + α·[**r + γ·V(s')** - V(s)]

> 🏃 **Pour mieux comprendre** : MC, c'est courir un marathon EN ENTIER puis regarder ton chrono pour t'évaluer. TD, c'est recalculer ton estimation de temps final à chaque kilomètre ("j'ai fait le km1 en 5min, je recalcule ma prévision"). TD est plus rapide mais moins précis.

---

### Question 10 (1 pt) — Qu'est-ce qu'un algorithme Actor-Critic ?

Un algorithme **Actor-Critic** combine deux composants qui apprennent simultanément :

- **Actor (Acteur)** : apprend la politique π_θ → "quelle action prendre dans cet état ?"
- **Critic (Critique)** : apprend la fonction de valeur V ou Q → "est-ce que cette action était bonne ?"

Le critic évalue les actions de l'actor via la **fonction avantage** A(s,a) = Q(s,a) - V(s), et l'actor ajuste sa politique en conséquence.

Exemples : A2C, A3C, PPO, SAC, TD3.

> 🎬 **Pour mieux comprendre** : C'est comme un acteur de cinéma (actor) qui joue une scène, et un réalisateur (critic) qui dit "C'était bien !" ou "On refait !". L'acteur s'améliore grâce aux retours du réalisateur.

---

### Question 11 (1 pt) — Exploration/Exploitation : le dilemme et une stratégie

**Le dilemme** :

- **Exploration** : essayer de nouvelles actions inconnues pour découvrir de meilleures stratégies
- **Exploitation** : choisir les actions connues comme les meilleures pour maximiser le gain immédiat

Le problème : trop d'exploration = on gaspille du temps. Trop d'exploitation = on reste coincé sur une solution sous-optimale.

**Stratégie proposée : ε-greedy**

```
Avec probabilité (1 - ε) : prendre l'action greedy argmax_a Q(s,a)  ← exploitation
Avec probabilité ε       : prendre une action aléatoire              ← exploration
```

- **Avantages** : simple à implémenter, garantit un minimum d'exploration, on peut faire décroître ε au fil du temps
- **Inconvénients** : explore uniformément (même les actions clairement mauvaises), le choix de ε est sensible

> 🍕 **Pour mieux comprendre** : 90% du temps tu vas à ta pizzeria préférée (exploitation), 10% tu essaies un nouveau restaurant (exploration). Au fil des années, tu baisses le 10% parce que tu connais tous les bons restos.

---

### Question 12 (1 pt) — Bootstrapping

Le **bootstrapping** consiste à mettre à jour une estimation en utilisant **une autre estimation** (plutôt que la vraie valeur).

**Exemple en TD learning** :

> V(s) ← V(s) + α·[r + γ·**V(s')** - V(s)]

Ici, **V(s')** est elle-même une estimation (pas la vraie valeur de s'). On "bootstrap" car on se fie à notre propre estimation pour en améliorer une autre.

**En Monte Carlo**, il n'y a PAS de bootstrapping : on utilise le vrai retour G(t) observé après un épisode complet.

**Conséquence** : le bootstrapping introduit un **biais** (on se fie à des estimations imparfaites) mais **réduit la variance** (on n'a pas besoin d'attendre la fin de l'épisode).

> 🎬 **Pour mieux comprendre** : C'est comme noter un film après 20 minutes en disant "ça ressemble au dernier film que j'ai vu, qui était un 7/10, donc j'estime celui-ci à 7". Tu utilises ta propre estimation passée (bootstrap) au lieu de regarder le film en entier.

---

### Question 13 (1 pt) — Replay Buffer

Un **Replay Buffer** est une mémoire (file/buffer) qui stocke les transitions passées **(s, a, r, s')**.

Au lieu d'apprendre uniquement sur la dernière transition, l'agent **tire aléatoirement un mini-batch** depuis cette mémoire pour s'entraîner.

**Pourquoi en utiliser :**

1. **Casse la corrélation temporelle** : les transitions consécutives sont corrélées ; le tirage aléatoire supprime cette corrélation → stabilise l'entraînement du réseau de neurones
2. **Réutilise les données** : chaque expérience peut être rejouée plusieurs fois → meilleure **sample efficiency**
3. **Indispensable pour l'off-policy** : permet d'apprendre à partir de données collectées par d'anciennes versions de la politique

> Utilisé dans : DQN, SAC, TD3. PAS dans : PPO, A2C (on-policy, données fraîches requises).

> 📓 **Pour mieux comprendre** : C'est comme un carnet d'erreurs qu'un étudiant relit aléatoirement pour réviser, au lieu de ne relire que le dernier cours. Il apprend plus vite et ne répète pas les mêmes erreurs.

---

# 2. Algorithmes de RL

## 2.1 Programmation Dynamique

---

### Question 14 (1 pt) — Le principe de la programmation dynamique (au-delà du RL)

La **programmation dynamique (DP)** est une méthode d'optimisation qui résout un problème complexe en le **décomposant en sous-problèmes plus simples** qui se chevauchent. On résout chaque sous-problème **une seule fois**, on **stocke** le résultat, et on le **réutilise** quand le même sous-problème réapparaît.

> 🧮 **Pour mieux comprendre** : Calculer fibonacci(50) en recalculant tout depuis zéro = catastrophe. Mais si tu stockes fib(48) et fib(49), alors fib(50) = fib(49) + fib(48) en 1 opération. C'est la DP : décomposer, stocker, réutiliser. En RL, Bellman fait pareil : V(s) = r + γ·V(s').

---

### Question 15 (1 pt) — Équation de Bellman pour V + pourquoi elle est utile

**L'équation de Bellman (Expectation)** :

> V^π(s) = E[r₀ + γ · V^π(s₁) | s₀ = s]

Forme développée :

> V^π(s) = Σ_a π(a|s) · [ Σ_r r·p(r|s,a) + γ · Σ_s' p(s'|s,a) · V^π(s') ]

**Interprétation** : La valeur d'un état = récompense immédiate attendue + γ × valeur attendue de l'état suivant.

**Pourquoi c'est utile** : Elle transforme un problème global (calculer la somme de TOUTES les récompenses futures sur une trajectoire infinie) en une **relation récursive locale** entre états voisins. Cela permet de calculer V(s) de manière **itérative** (point fixe) sans explorer toutes les trajectoires possibles — c'est la base de tous les algorithmes DP.

> 🏠 **Pour mieux comprendre** : La valeur d'un appart = le loyer de ce mois + γ × la valeur de l'appart le mois prochain. Tu n'as pas besoin de calculer 50 ans de loyers, juste le loyer actuel et la valeur au mois suivant.

---

### Question 16 (1 pt) — Calcul de V manquante (γ = 1)

Diagramme du MDP - Question 16

**Données du diagramme** — C'est un MRP (Markov Reward Process) avec γ = 1 :


| État                | V(s) donné | R(s) | Transitions sortantes                      |
| ------------------- | ---------- | ---- | ------------------------------------------ |
| □ (carré, terminal) | **0**      | 0    | —                                          |
| ○ haut-gauche       | **-23**    | -1   | 0.9 → lui-même, 0.1 → (-13)                |
| ○ milieu-gauche     | **-13**    | -2   | 0.5 → (-23), 0.5 → (1.5)                   |
| ○ milieu            | **1.5**    | -2   | 0.8 → (?), 0.2 → (0 terminal)              |
| ○ milieu-droit      | **?**      | -2   | 0.6 → (10), 0.4 → (+0.8)                   |
| ○ droite            | **10**     | +10  | 1.0 → (0 terminal)                         |
| ○ bas               | **+0.8**   | +1   | 0.4 → (?), 0.4 → (0 terminal), 0.2 → (-13) |


**Méthode** : On applique l'équation de Bellman au noeud "?" avec γ = 1 :

> V(s) = R(s) + γ · Σ_s' P(s'|s) · V(s')

Les états "?" et "+0.8" dépendent l'un de l'autre, donc on résout un **système de 2 équations à 2 inconnues**. On pose x = V(?) et on utilise V(+0.8) = 0.8 donné, ou mieux on vérifie par le système :

**Équation pour "?"** : x = V(?)

> x = -2 + 1 × [0.6 × 10 + 0.4 × V(+0.8)]
> x = -2 + 6 + 0.4 × V(+0.8)
> x = 4 + 0.4 × V(+0.8) ... (I)

**Équation pour "+0.8"** : y = V(+0.8)

> y = 1 + 1 × [0.4 × x + 0.4 × 0 + 0.2 × (-13)]
> y = 1 + 0.4x + 0 - 2.6
> y = -1.6 + 0.4x ... (II)

**Résolution** — on substitue (II) dans (I) :

> x = 4 + 0.4 × (-1.6 + 0.4x)
> x = 4 - 0.64 + 0.16x
> x - 0.16x = 3.36
> 0.84x = 3.36

> **x = V(?) = 4**

Vérification : V(+0.8) = -1.6 + 0.4 × 4 = **0** (si on prend les transitions telles que lues).

> ⚠️ **Note** : la valeur exacte dépend de la lecture précise des flèches du diagramme. La **méthode** est toujours la même :
>
> 1. Écrire l'équation de Bellman pour le noeud "?"
> 2. Substituer les V connus des états voisins
> 3. Résoudre (système linéaire si interdépendance)
>
> Pour un examen, l'essentiel est de **montrer la démarche** : Bellman → substitution → résolution.

---

### Question 17 (3 pts) — Pseudo-code d'un algorithme DP

**Value Iteration** (le plus simple et le plus court à écrire) :

```
Entrée : MDP (S, A, P, R, γ)
Initialiser V(s) = 0 pour tout s ∈ S

Répéter jusqu'à convergence (max|V_new(s) - V_old(s)| < seuil) :
    Pour chaque état s ∈ S :
        V(s) ← max_a [ Σ_r r·p(r|s,a) + γ · Σ_s' p(s'|s,a) · V(s') ]

Extraire la politique optimale :
    Pour chaque état s ∈ S :
        π*(s) = argmax_a [ Σ_r r·p(r|s,a) + γ · Σ_s' p(s'|s,a) · V(s') ]

Retourner V*, π*
```

**Explication** :

- On initialise toutes les valeurs à 0
- À chaque itération, pour chaque état, on prend la **meilleure action possible** (max) et on met à jour V
- On répète jusqu'à ce que les valeurs ne changent plus (convergence)
- Puis on en déduit la politique optimale

**Ce qui fait la différence pour les 3 pts** : pseudo-code clair + explication des étapes + mention du critère d'arrêt.

> 🗺️ **Pour mieux comprendre** : Imagine un labyrinthe. Au début, toutes les cases valent 0. À chaque tour, tu mets à jour chaque case : "si je prends le meilleur chemin depuis ici, qu'est-ce que ça vaut ?". Après quelques tours, la valeur de chaque case s'est stabilisée et le chemin optimal apparaît naturellement.

---

### Question 18 (1 pt) — Avantages et inconvénients de la DP


| Avantages                                          | Inconvénients                                                                                                       |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Convergence garantie** vers la solution optimale | Nécessite le **modèle complet** de l'environnement (P et R connus)                                                  |
| Fournit une solution **exacte**                    | **Coûteux en calcul** pour les grands espaces d'états (curse of dimensionality)                                     |
| Base théorique solide                              | C'est un algorithme de **planification**, pas d'apprentissage : ne gère pas l'exploration d'environnements inconnus |


---

### Question 19 (3 pts BONUS) — Preuve de l'équation de Bellman

**Définitions** :

- G = r₀ + γr₁ + γ²r₂ + ... = gain total (somme actualisée des récompenses)
- V^π(s) = E[G | s₀ = s] = valeur de l'état s sous la politique π
- G₁ = r₁ + γr₂ + ... = gain à partir de l'état s₁

**Preuve** :

Par définition du gain : G = r₀ + γ·G₁

Donc :

> V^π(s) = E[G | s₀=s] = E[r₀ + γ·G₁ | s₀=s] = E[r₀ | s₀=s] + γ·E[G₁ | s₀=s]

Par la **loi de l'espérance totale** (espérance conditionnelle itérée / Fubini) :

> E[G₁ | s₀=s] = E[ E[G₁ | s₀=s, s₁] | s₀=s ]

Par la **propriété de Markov** (le futur ne dépend que de l'état présent) :

> E[G₁ | s₀=s, s₁] = E[G₁ | s₁] = V^π(s₁)

On conclut :

> **V^π(s) = E[r₀ + γ·V^π(s₁) | s₀=s]**     ∎

**Théorèmes utilisés** :

1. **Loi de l'espérance totale** (tower rule / espérance conditionnelle itérée)
2. **Propriété de Markov** du MDP

**Convergence de l'itération** : L'opérateur de Bellman est **contractant** de facteur γ pour la norme sup → le **théorème de Banach** (point fixe) garantit l'existence et l'unicité de la solution, ainsi que la convergence de l'itération.

---

## 2.2 Méthodes Model-Free

---

### Question 20 — Deux politiques déterministes d'exploration en environnement discret

1. **ε-greedy** : avec probabilité (1-ε) on prend l'action greedy (meilleure connue), avec probabilité ε on prend une action aléatoire uniforme
2. **Softmax (Boltzmann)** : on choisit chaque action avec une probabilité proportionnelle à exp(Q(s,a)/τ) — les actions à haute valeur Q sont plus probables, mais toutes les actions ont une chance

> Note : l'ε-greedy et le softmax sont techniquement stochastiques. Si le prof attend vraiment "déterministes", on pourrait citer : (1) **Greedy pur** (toujours la meilleure action connue, pas d'exploration) et (2) **Round-robin / exploration systématique** (tester chaque action à tour de rôle dans chaque état).

---

### Question 21 — Identification d'un algorithme (pseudo-code dans le sujet)

Le pseudo-code montré dans le sujet correspond très probablement à **Q-Learning** (mise à jour avec max).

**(a) Nom : Q-Learning**

**(b) Value-based ou Policy-based ?**

C'est **value-based**. Justification : l'algorithme apprend directement la fonction Q(s,a) (action-value function). La politique n'est pas explicitement représentée ; elle est simplement **dérivée** de Q en prenant argmax_a Q(s,a). Il n'y a pas de réseau de politique séparé.

**(c) TD ou Monte Carlo ?**

C'est basé sur **TD (Temporal Difference)**. Justification : la mise à jour utilise le **bootstrapping** :

> Q(s,a) ← Q(s,a) + α·[r + γ·max_a' Q(s', a') - Q(s,a)]

Le terme `r + γ·max_a' Q(s', a')` est une estimation (TD target) qui utilise Q(s', a') — une autre estimation — au lieu d'attendre le retour complet G(t) comme en Monte Carlo.

> 🌊 **Pour mieux comprendre** : Q-Learning, c'est comme traverser une rivière. À chaque pierre (pas), tu mets à jour ta carte mentale : "cette pierre + la meilleure pierre après = mon estimation du chemin". Tu n'attends pas d'avoir traversé toute la rivière (MC) pour apprendre.

---

## 2.3 Cas Continu

---

### Question 22 (1 pt) — Exemple concret de RL en continu

**Contrôle d'un bras robotique** : les angles des articulations sont des **états continus** (dans ℝⁿ) et les couples/forces à appliquer sont des **actions continues** (dans ℝᵐ). L'objectif est d'atteindre une cible.

Autres exemples acceptables :

- Voiture autonome (vitesse et angle de braquage continus)
- Contrôle de drone (angles, poussée)
- Trading financier (montants d'investissement continus)
- Course de drone (mentionné dans le cours 7)

---

### Question 23 (1 pt) — Principaux défis du cas continu

1. **Impossible de tabuler** V(s) ou Q(s,a) : les espaces d'états et d'actions sont infinis, on ne peut pas stocker une valeur pour chaque combinaison
2. **Nécessite de la généralisation** : il faut pouvoir estimer la valeur d'états/actions **jamais visités**
3. **Exploration beaucoup plus difficile** : l'espace est infini, impossible de tout explorer exhaustivement
4. Les algorithmes tabulaires classiques (Q-Learning avec tableau) **ne s'appliquent plus directement**

> 🚗 **Pour mieux comprendre** : En discret (labyrinthe 4×4), tu as 16 cases = 16 valeurs à stocker. Pour une voiture autonome, tu as une infinité de positions × une infinité de vitesses × une infinité d'angles... Impossible de faire un tableau.

---

### Question 24 (1 pt) — Outil pour adapter les méthodes discrètes au continu

L'**approximation de fonction** (Function Approximation) :

- On **paramétrise** V_θ(s), Q_θ(s,a) ou π_θ(a|s) par un **réseau de neurones** (ou une fonction linéaire)
- Au lieu d'un tableau Q[s][a], on a une **fonction** Q_θ(s,a) qui prend s en entrée et produit les Q-values
- Le réseau apprend à **généraliser** : il peut estimer la valeur d'états qu'il n'a jamais vus, en interpolant à partir des états déjà rencontrés

---

### Question 25 (1 pt) — Un algorithme pour le cas continu

**PPO (Proximal Policy Optimization)** :

- **Actor-Critic**, on-policy
- La politique π_θ est un réseau de neurones qui sort une **distribution continue** (ex: gaussienne avec moyenne μ et écart-type σ appris)
- Utilise le **clipped surrogate objective** pour limiter les mises à jour de la politique (stabilité)
- Ajoute un **bonus d'entropie** pour encourager l'exploration
- Très populaire en pratique (utilisé pour RLHF dans ChatGPT)

Autres réponses acceptables : **SAC** (Soft Actor-Critic, off-policy, maximum entropy), **TD3** (Twin Delayed DDPG, off-policy, déterministe), **DDPG** (Deep Deterministic Policy Gradient).

---

## 2.4 Deep Reinforcement Learning

---

### Question 26 (1 pt) — Intérêt principal des réseaux de neurones en RL

Les réseaux de neurones servent d'**approximateurs de fonctions universels** qui permettent de :

1. Représenter V(s), Q(s,a) ou π(a|s) dans des **espaces très grands ou continus** (là où les tableaux sont impossibles)
2. **Généraliser** à des états/actions jamais rencontrés pendant l'entraînement
3. Traiter des **entrées de haute dimension** comme des images (pixels), du texte, ou des signaux complexes

Sans eux, on est limité aux petits environnements discrets avec des tables Q[s][a].

> 📊 **Pour mieux comprendre** : Sans réseau de neurones = un tableur Excel où tu stockes une valeur par case. Ça marche pour 100 cases (labyrinthe) mais pas pour 10^170 positions (Go). Le réseau de neurones est une fonction compacte qui "résume" ce tableur géant et devine les cases qu'il n'a jamais vues.

---

### Question 27 (1 pt) — Fonction à optimiser pour π_θ

**Nom** : La **fonction objectif** (ou fonction d'utilité / expected return)

**Définition** :

> J(θ) = E_{τ ~ π_θ} [R(τ)]

C'est l'**espérance du retour cumulé** sur les trajectoires τ générées par la politique paramétrée π_θ.

**Optimisation** : par **montée de gradient** (gradient ascent) :

> θ ← θ + α · ∇_θ J(θ)

On cherche les paramètres θ qui maximisent le retour moyen.

---

### Question 28 (1 pt) — Architecture DQN : entrées et sorties

**Architecture** : un **réseau de neurones profond** (Deep Neural Network)

- **Entrée** : l'**état s** (ex: les 4 dernières images écran empilées pour Atari, ou un vecteur de features)
- **Sortie** : un **vecteur de Q-values**, une par action possible → [Q(s,a₁), Q(s,a₂), ..., Q(s,aₙ)]
- **Action choisie** : argmax sur les sorties (l'action avec la plus grande Q-value)

```
État s → [Réseau de neurones] → Q(s,a₁), Q(s,a₂), ..., Q(s,aₙ)
                                        ↓
                                 Action = argmax
```

> 🕹️ **Pour mieux comprendre** : Pour Atari Breakout, DQN "voit" l'écran (pixels en entrée), et sort un score pour chaque bouton : "gauche = 12, droite = 3, rien = 1". Il appuie sur gauche.

---

### Question 29 — Algorithme de policy gradient avec réseau de neurones

Le pseudo-code montré correspond à **REINFORCE** (Monte-Carlo Policy Gradient).

**(a) Quel théorème ?**

Le **Policy Gradient Theorem**. Il dit que :

> ∇_θ J(θ) = E [ Σ_t ∇_θ log π_θ(a_t|s_t) · G_t ]

Ce théorème permet de calculer le gradient de la performance **sans connaître le modèle de l'environnement** (pas besoin de P). On n'a besoin que de trajectoires observées.

**(b) Avantage d'utiliser γ ?**

L'utilisation de γ (discount factor) dans le calcul du gain G_t **réduit la variance** des estimations du gradient.

Sans γ (ou γ=1), les récompenses très lointaines ajoutent beaucoup de bruit (variance) car elles dépendent de nombreuses actions aléatoires. Avec γ < 1, on pondère moins les récompenses lointaines, ce qui **stabilise et accélère l'apprentissage**, au prix d'un léger biais.

**(c) Limitation concrète et amélioration ?**

**Limitation** : REINFORCE souffre d'une **haute variance** car il utilise le retour Monte Carlo complet G_t. Les estimations de gradient sont bruitées → convergence lente, instable, nécessite beaucoup de trajectoires.

**Cas concret** : dans un environnement avec des épisodes longs (ex: un jeu qui dure 10 000 pas), le retour G_t varie énormément d'un épisode à l'autre → le gradient est très bruité.

**Amélioration** : Ajouter une **baseline** (typiquement la fonction de valeur V(s)) et utiliser la **fonction avantage** A(s,a) = G_t - V(s) au lieu de G_t seul. Cela mène aux méthodes **Actor-Critic (A2C)** qui réduisent drastiquement la variance tout en restant non biaisées.

**(d) BONUS — Nom de l'algorithme ?**

**REINFORCE** (aussi appelé Monte-Carlo Policy Gradient).

> 🏀 **Pour mieux comprendre** : REINFORCE, c'est comme apprendre le lancer franc en lançant 100 fois, puis en renforçant les techniques qui ont marché (panier rentré) et en évitant celles qui ont raté. Le problème : si tu lances 100 fois, c'est très aléatoire (haute variance). L'amélioration (A2C) = ajouter un coach (critic) qui te dit "ce lancer était mieux/pire que ta moyenne" → tu progresses plus vite.

---

### Question 30 (1 pt) — 3 caractéristiques de PPO

1. **Actor-Critic** : utilise un réseau acteur (politique π_θ) et un réseau critique (fonction de valeur V_φ)
2. **Clipped surrogate objective** (gradient clipping) : limite les mises à jour de la politique pour éviter des changements trop brusques → stabilité de l'apprentissage (inspiré de la trust region de TRPO)
3. **Bonus d'entropie** : ajoute un terme d'entropie dans la loss pour encourager l'exploration et éviter la convergence prématurée vers une politique déterministe

> Autres caractéristiques notables : on-policy, très populaire (utilisé pour RLHF/ChatGPT), plus rapide en wall-clock time que SAC/TD3 même si moins sample-efficient.

---

## 2.5 Model-Based RL

---

### Question 31 (1 pt) — Motivation principale du Model-Based

La principale motivation est la **sample efficiency** (efficacité d'échantillonnage) : en ayant un modèle de l'environnement (même appris), l'agent peut **planifier** et **simuler** des trajectoires mentalement, sans interagir réellement avec l'environnement. Cela réduit drastiquement le nombre d'interactions réelles nécessaires pour apprendre une bonne politique.

> 🧠 **Pour mieux comprendre** : Un joueur d'échecs qui "joue les coups dans sa tête" avant de toucher une pièce (model-based) apprend beaucoup plus vite qu'un joueur qui ne fait que jouer des parties réelles sans réfléchir à l'avance (model-free). Il économise du temps et des parties réelles.

---

### Question 32 (1 pt) — Principale difficulté du Model-Based

**Apprendre un modèle fidèle** de l'environnement est très difficile. Les erreurs de modélisation **s'accumulent** au fil des pas de planification (compound error / error accumulation) : une petite erreur sur le modèle à chaque pas se propage et s'amplifie sur des trajectoires longues, menant à des plans sous-optimaux voire complètement faux.

---

### Question 33 (1 pt BONUS) — Exemple d'algorithme Model-Based + application concrète

**MuZero** (DeepMind, 2020) :

- Apprend un **modèle interne** (en représentation latente) qui prédit trois choses : la **valeur**, la **politique**, et la **récompense**
- Utilise **MCTS** (Monte Carlo Tree Search) pour planifier à partir de ce modèle
- **Application concrète** : MuZero a atteint des performances surhumaines sur les jeux **Atari** et le jeu de **Go**, le tout **sans connaître les règles du jeu** à l'avance (contrairement à AlphaGo qui avait les règles codées en dur)

**Lignée historique** :
AlphaGo (règles + données humaines) → AlphaZero (règles seulement, self-play) → MuZero (pas de règles) → EfficientZero (encore plus sample-efficient)

---

# Récapitulatif : les points "faciles" vs "difficiles"


| Catégorie                     | Questions      | Points           | Difficulté    |
| ----------------------------- | -------------- | ---------------- | ------------- |
| Définitions pures (par coeur) | Q1-5, Q14      | 8 pts            | ★☆☆ Facile    |
| Distinctions conceptuelles    | Q6-13          | 8 pts            | ★☆☆ Facile    |
| Équation de Bellman           | Q15-16         | 2 pts            | ★★☆ Moyen     |
| Pseudo-code DP                | Q17            | 3 pts            | ★★☆ Moyen     |
| Avantages/inconvénients       | Q18            | 1 pt             | ★☆☆ Facile    |
| Model-free (identification)   | Q20-21         | 4 pts            | ★★☆ Moyen     |
| Cas continu                   | Q22-25         | 4 pts            | ★☆☆ Facile    |
| Deep RL                       | Q26-30         | 7 pts            | ★★☆ Moyen     |
| Model-Based                   | Q31-32         | 2 pts            | ★☆☆ Facile    |
| **Bonus**                     | Q19, Q29d, Q33 | 4 pts            | ★★★ Difficile |
| **TOTAL**                     |                | **40 + 4 bonus** |               |


> **Stratégie** : Les 16 premiers points (définitions + distinctions) sont du par-coeur pur et se ramassent en 30 min. Les 8 points de Q17 + Q15-16 + Q18 demandent de comprendre Bellman et DP. Le reste (continu, deep RL, model-based) est de la compréhension générale. Les bonus nécessitent un vrai travail de fond (preuve, identification fine).

