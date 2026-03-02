J'ai maintenant lu l'intégralité de vos supports de cours et l'examen. Voici une analyse complète et un guide de préparation structuré.

---

# Préparation au DS de Reinforcement Learning (EFREI 2024/2025)

## Structure de l'examen

L'examen dure **1h50** et vaut **40 points** (+ 4 pts bonus). Il est structuré en 5 parties :

| Partie | Thème | Points |
|--------|-------|--------|
| 1.1 | Définitions générales | 9 pts |
| 1.2 | Concepts généraux | 8 pts |
| 2.1 | Programmation Dynamique | 7 pts (+3 bonus) |
| 2.2 | Méthodes Model-free | 3 pts |
| 2.3 | Cas Continu | 4 pts |
| 2.4 | Deep RL | 6 pts (+1 bonus) |
| 2.5 | Model-based RL | 2 pts (+1 bonus) |

---

## PARTIE 1 : GENERALITES - Réponses types

### Q1 (3 pts) - Schéma du cadre RL et variables

Le cadre général :

```
Agent <---> Environnement
  - Agent observe l'état S(t), reçoit la récompense R(t), exécute l'action A(t)
  - Environnement reçoit A(t), émet S(t+1) et R(t+1)
```

**Variables principales :**
- **Agent** : le système qui prend des décisions
- **Environnement** : le système externe avec lequel l'agent interagit
- **État S(t)** : information décrivant la situation actuelle
- **Action A(t)** : décision prise par l'agent dans un état donné
- **Récompense R(t)** : feedback évaluant l'action prise

### Q2 (1 pt) - MDP

Un **Markov Decision Process** est un cadre mathématique pour le RL. C'est un tuple **(S, A, P, R)** :
- **S** : ensemble fini d'états
- **A** : ensemble fini d'actions
- **P** : probabilités de transition \( p(s'|s,a) \)
- **R** : fonction de récompense \( p(r|s,a) \)

Il satisfait la **propriété de Markov** : l'état futur ne dépend que de l'état présent, pas du passé.

### Q3a (1 pt) - Value Function V

\[ V^\pi(s) = \mathbb{E}[G \mid s_0 = s] \]

C'est l'espérance du gain cumulé en partant de l'état s sous la politique \(\pi\).

### Q3b (1 pt) - Action-Value Function Q

\[ Q^\pi(s,a) = \mathbb{E}[G \mid s_0 = s, a_0 = a] \]

C'est l'espérance du gain cumulé en partant de l'état s, en prenant l'action a, puis en suivant \(\pi\).

### Q3c (1 pt) - Gain G

\[ G_t = r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \ldots = \sum_{k=0}^{\infty} \gamma^k r_{t+k} \]

C'est la somme pondérée (discountée) des récompenses futures.

### Q4 (1 pt) - Discount Factor \(\gamma\)

Le **facteur d'actualisation** \(\gamma \in [0,1]\) contrôle l'importance des récompenses futures :
- Si \(\gamma = 0\) : l'agent ne considère que la récompense immédiate
- Si \(\gamma \to 1\) : les récompenses futures comptent autant que les immédiates
- Il assure la **convergence** du gain quand il n'y a pas d'état terminal

### Q5 (1 pt) - Fonction de récompense efficace

Une bonne fonction de récompense doit **encourager le comportement souhaité** et **pénaliser les comportements indésirables**.

**Exemple concret** : Robot marcheur
- \(+1\) pour chaque pas en avant (encourage le mouvement)
- \(-1\) pour une chute (pénalise l'instabilité)

---

## PARTIE 1.2 : CONCEPTS GENERAUX

### Q6 (1 pt) - Model-Free vs Model-Based

| Model-Free | Model-Based |
|------------|-------------|
| Apprend directement depuis l'interaction | Construit/utilise un modèle de l'environnement |
| Pas besoin de connaître P et R | Nécessite ou apprend les probabilités de transition |
| Ex: Q-Learning, SARSA | Ex: AlphaZero, MuZero |

### Q7 (1 pt) - Policy-based vs Value-based

| Policy-based | Value-based |
|-------------|-------------|
| Optimise directement la politique \(\pi\) | Calcule une fonction de valeur V ou Q |
| Pas de fonction de valeur | La politique est dérivée de la valeur (greedy) |
| Ex: REINFORCE, PPO | Ex: Q-Learning, DQN |

### Q8 (1 pt) - Off-Policy vs On-Policy

| On-Policy | Off-Policy |
|-----------|-----------|
| Apprend en suivant la politique actuelle | Apprend à partir de données d'une autre politique |
| La politique d'exploration = politique cible | Politique comportementale ≠ politique cible |
| Ex: SARSA, A2C | Ex: Q-Learning, DQN |

### Q9 (1 pt) - TD vs Monte Carlo

| TD (Temporal Difference) | Monte Carlo |
|--------------------------|-------------|
| Met à jour à **chaque pas** | Met à jour à la **fin de l'épisode** |
| Utilise le **bootstrapping** | Utilise le **retour complet** G(t) |
| **Biaisé** mais faible variance | **Non biaisé** mais haute variance |
| Converge généralement plus vite | Nécessite des épisodes complets |

### Q10 (1 pt) - Actor-Critic

Un algorithme **Actor-Critic** combine :
- **Actor (Acteur)** : apprend la politique \(\pi_\theta\) (quelle action prendre)
- **Critic (Critique)** : apprend la fonction de valeur V ou Q (évalue la qualité de l'action)

Le critic guide l'actor en fournissant une estimation de la qualité des actions, réduisant ainsi la variance par rapport aux méthodes purement policy-gradient.

### Q11 (1 pt) - Exploration vs Exploitation

- **Exploration** : essayer de nouvelles actions pour découvrir de meilleures récompenses
- **Exploitation** : choisir les actions connues comme les meilleures

**Stratégie : \(\varepsilon\)-greedy**
- Avec probabilité \(1-\varepsilon\) : action greedy (exploitation)
- Avec probabilité \(\varepsilon\) : action aléatoire (exploration)
- **Avantage** : simple, efficace, garantit l'exploration
- **Inconvénient** : explore uniformément, ne favorise pas les actions prometteuses

### Q12 (1 pt) - Bootstrapping

Le **bootstrapping** consiste à mettre à jour une estimation en utilisant **d'autres estimations** plutôt que les vraies valeurs. En TD learning :

\[ V(s_t) \leftarrow V(s_t) + \alpha[r_t + \gamma V(s_{t+1}) - V(s_t)] \]

On utilise \(V(s_{t+1})\) (une estimation) pour mettre à jour \(V(s_t)\), au lieu d'attendre le retour complet comme en Monte Carlo.

### Q13 (1 pt) - Replay Buffer

Un **Replay Buffer** est une mémoire qui stocke les transitions \((s, a, r, s')\) passées. L'agent y pioche aléatoirement des mini-batches pour s'entraîner.

**Avantages :**
- Casse la corrélation temporelle entre les données
- Meilleure efficacité d'échantillonnage (réutilise les expériences)
- Stabilise l'apprentissage des réseaux de neurones

---

## PARTIE 2.1 : PROGRAMMATION DYNAMIQUE

### Q14 (1 pt) - Principe de la DP

La **programmation dynamique** résout des problèmes complexes en les **décomposant en sous-problèmes plus simples**, en exploitant la **structure récursive** du problème. On résout les sous-problèmes une fois et on stocke les résultats.

### Q15 (1 pt) - Equation de Bellman

\[ V^\pi(s) = \mathbb{E}[r_0 + \gamma V^\pi(s_1) \mid s_0 = s] = \sum_a \pi(a|s) \left[ \sum_r r \cdot p(r|s,a) + \gamma \sum_{s'} V^\pi(s') \cdot p(s'|s,a) \right] \]

**Utilité** : Elle exprime la valeur d'un état comme la récompense immédiate + la valeur actualisée de l'état suivant. Elle permet de calculer V de manière itérative (point fixe) sans énumérer toutes les trajectoires.

### Q16 (1 pt) - Calcul de V manquante

(C'est un exercice avec un diagramme de MDP dans le sujet -- il faut appliquer l'équation de Bellman avec \(\gamma=1\).)

### Q17 (3 pts) - Pseudo-code d'un algorithme DP

**Value Iteration** :

```
Initialiser V(s) arbitrairement pour tout s
Répéter jusqu'à convergence:
    Pour chaque état s:
        V(s) <- max_a [ sum_r r*p(r|s,a) + gamma * sum_s' p(s'|s,a) * V(s') ]
Extraire la politique:
    pi(s) = argmax_a [ sum_r r*p(r|s,a) + gamma * sum_s' p(s'|s,a) * V(s') ]
```

**Ou Policy Iteration** :

```
Initialiser pi(s) arbitrairement
Répéter jusqu'à convergence:
    1. Policy Evaluation: Résoudre V_pi via l'eq. de Bellman
    2. Policy Improvement: Pour chaque s,
       pi(s) <- argmax_a [E(r + gamma*V_pi(s') | s, a)]
```

### Q18 (1 pt) - Avantages/Inconvénients de la DP

| Avantages | Inconvénients |
|-----------|---------------|
| Convergence garantie | Nécessite un modèle complet (P, R) |
| Solution exacte | Coûteux pour grands espaces d'états |
| Base théorique solide | Ne gère pas l'exploration |

### Q19 (3 pts bonus) - Preuve de Bellman

Par définition \( G = r_0 + \gamma G_1 \), donc :
\[ V^\pi(s) = \mathbb{E}[G|s_0=s] = \mathbb{E}[r_0 + \gamma G_1|s_0=s] = \mathbb{E}[r_0|s_0=s] + \gamma\mathbb{E}[G_1|s_0=s] \]

Par espérance conditionnelle et propriété de Markov :
\[ \mathbb{E}[G_1|s_0=s] = \mathbb{E}[\mathbb{E}[G_1|s_0=s, s_1]|s_0=s] = \mathbb{E}[V^\pi(s_1)|s_0=s] \]

Donc \( V^\pi(s) = \mathbb{E}[r_0 + \gamma V^\pi(s_1)|s_0=s] \). CQFD.

---

## PARTIE 2.2 : METHODES MODEL-FREE

### Q20 - Deux politiques d'exploration déterministes

1. **\(\varepsilon\)-greedy** : avec probabilité \(1-\varepsilon\) prend l'action greedy, sinon aléatoire
2. **Softmax (Boltzmann)** : sélection pondérée par les valeurs Q via une distribution softmax

*(Note: l'epsilon-greedy est techniquement stochastique. Des alternatives déterministes pourraient être : exploration systématique round-robin, ou Upper Confidence Bound UCB.)*

### Q21 - Identification d'algorithme (pseudo-code dans le sujet)

D'après le cours, le pseudo-code montré est très probablement **Q-Learning** :
- **(a)** Nom : **Q-Learning**
- **(b)** C'est **value-based** car il apprend directement la fonction Q sans représenter explicitement la politique
- **(c)** C'est basé sur **TD** (Temporal Difference) car il met à jour Q à chaque pas avec bootstrapping : \( Q(s,a) \leftarrow Q(s,a) + \alpha[r + \gamma \max_{a'} Q(s',a') - Q(s,a)] \)

---

## PARTIE 2.3 : CAS CONTINU

### Q22 (1 pt) - Exemple d'application continue

**Contrôle robotique** : un bras robotique avec des angles de joints continus (états) qui doit appliquer des couples continus (actions) pour atteindre une cible.

Autres exemples : voiture autonome, drone, trading.

### Q23 (1 pt) - Défis du cas continu

- **Espaces d'états/actions infinis** : impossible de stocker V(s) ou Q(s,a) dans un tableau
- **Généralisation nécessaire** : il faut pouvoir estimer la valeur d'états jamais vus
- **Exploration plus difficile** : l'espace est trop grand pour être exploré exhaustivement

### Q24 (1 pt) - Outil pour adapter les méthodes discrètes au continu

L'**approximation de fonction** (Function Approximation) : on paramétrise V ou Q par un réseau de neurones ou une fonction linéaire qui peut généraliser à des états non visités.

### Q25 (1 pt) - Algorithme pour le cas continu

**PPO (Proximal Policy Optimization)** : algorithme actor-critic, on-policy, qui optimise directement la politique paramétrée \(\pi_\theta\). Il utilise le gradient clipping pour limiter les mises à jour de la politique et assurer la stabilité. Combine l'avantage de A2C (fonction avantage) et la région de confiance de TRPO.

*(Autres réponses acceptables : SAC, TD3, DDPG)*

---

## PARTIE 2.4 : DEEP RL

### Q26 (1 pt) - Intérêt des réseaux de neurones en RL

Permettre l'**approximation de fonctions** dans des espaces d'états/actions très grands ou continus. Le réseau de neurones peut **généraliser** à des états non visités et **représenter** des fonctions de valeur ou des politiques complexes.

### Q27 (1 pt) - Fonction à optimiser pour \(\pi_\theta\)

La **fonction objectif** (ou fonction d'utilité) :

\[ J(\theta) = \mathbb{E}_{\tau \sim \pi_\theta}[R(\tau)] \]

C'est l'espérance du retour cumulé sur les trajectoires générées par la politique paramétrée. On l'optimise par **montée de gradient** (gradient ascent).

### Q28 (1 pt) - Architecture DQN

- **Architecture** : Réseau de neurones profond (Deep Neural Network)
- **Entrée** : l'état s (ex: pixels de l'écran pour Atari)
- **Sortie** : les valeurs Q(s, a) pour chaque action possible

Le réseau prend un état et retourne un vecteur de Q-values, une par action.

### Q29 - Pseudo-code d'un algorithme Policy Gradient

- **(a)** On utilise le **Policy Gradient Theorem** qui permet de calculer le gradient de J(θ) sans connaître la dynamique de l'environnement
- **(b)** L'avantage d'utiliser \(\gamma\) : il **réduit la variance** en pondérant moins les récompenses lointaines, tout en gardant un biais acceptable. Il favorise les récompenses proches et stabilise l'apprentissage.
- **(c)** **Limitation** : haute variance des estimations, convergence vers un optimum local. **Amélioration** : utiliser une **baseline** (fonction avantage A(s,a) = Q(s,a) - V(s)) pour réduire la variance, ce qui mène aux méthodes **Actor-Critic** (A2C).
- **(d) (Bonus)** : **REINFORCE** (Monte-Carlo Policy Gradient)

### Q30 (1 pt) - 3 caractéristiques de PPO

1. **Algorithme Actor-Critic** : utilise un acteur (politique) et un critique (fonction de valeur)
2. **Gradient Clipping** : utilise un objectif surrogate clippé pour limiter les mises à jour et éviter les chutes catastrophiques de performance
3. **Bonus d'entropie** pour encourager l'exploration

---

## PARTIE 2.5 : MODEL-BASED RL

### Q31 (1 pt) - Motivation du Model-Based

Permettre de **planifier** en simulant des trajectoires futures, ce qui améliore drastiquement l'**efficacité d'échantillonnage** (sample efficiency). L'agent peut apprendre avec beaucoup moins d'interactions réelles avec l'environnement.

### Q32 (1 pt) - Principale difficulté

La **construction/apprentissage d'un modèle fidèle** de l'environnement. Les erreurs du modèle s'accumulent sur les trajectoires longues, menant à des plans sous-optimaux (compound error).

### Q33 (1 pt bonus) - Exemple d'algorithme Model-Based

**MuZero** (DeepMind) : apprend un modèle interne de l'environnement (valeur, politique, récompense) combiné avec MCTS (Monte Carlo Tree Search) pour planifier. Appliqué avec succès aux jeux Atari et au jeu de Go, sans avoir besoin de règles codées en dur.

---

## Conseils de révision prioritaires

1. **Savoir redessiner de mémoire** le schéma Agent-Environnement avec toutes les variables
2. **Maîtriser l'équation de Bellman** et savoir l'appliquer sur un petit MDP (calcul numérique)
3. **Connaître par coeur** les différences : TD vs MC, On-policy vs Off-policy, Model-free vs Model-based, Value-based vs Policy-based
4. **Savoir écrire le pseudo-code** de Value Iteration OU Policy Iteration
5. **Comprendre Q-Learning vs SARSA** : la différence est dans la règle de mise à jour (max vs action suivante réelle)
6. **Retenir les caractéristiques** de PPO, DQN, REINFORCE, A2C
7. **Savoir expliquer** le Policy Gradient Theorem et la fonction avantage

Les questions les plus rentables en points sont les **Q1 (3pts)**, **Q17 (3pts)**, et les définitions de la partie 1 qui représentent ~17 points de questions "par coeur". Concentrez-vous d'abord là-dessus !