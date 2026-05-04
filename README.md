# 🧠 AI/ML From Scratch — Daily Notes

> A personal learning journal documenting my journey through AI & ML fundamentals — one concept at a time.

![Progress](https://img.shields.io/badge/Status-Active-brightgreen)
![Topics](https://img.shields.io/badge/Topics%20Covered-8-blue)
![Learning](https://img.shields.io/badge/Learning-Daily-orange)

---

## 📚 Table of Contents

- [🤖 What is an Agent?](#-what-is-an-agent)
- [🗺️ State](#️-state)
- [🚀 Initial State](#-initial-state)
- [⚡ Actions](#-actions)
- [🔄 Transition Model](#-transition-model)
- [🌐 State Space](#-state-space)
- [🎯 Goal Test](#-goal-test)
- [💰 Path Cost Function](#-path-cost-function)
- [🧩 Putting It All Together](#-putting-it-all-together)
- [📅 Daily Log](#-daily-log)

---

## 🤖 What is an Agent?

> *"Anything that perceives its environment through sensors and acts upon it through actuators."*
> — Russell & Norvig, AIMA

An **agent** is the core building block of AI systems. Think of it as anything that can:

1. **Perceive** — sense the world around it (via sensors, inputs, data)
2. **Think** — process what it perceives using some decision-making logic
3. **Act** — take actions that affect the environment (via actuators, outputs)

### Types of Agents

| Agent Type | Description | Example |
|---|---|---|
| **Simple Reflex** | Acts only on current percept | Thermostat |
| **Model-Based Reflex** | Keeps track of world state | Robot vacuum with memory |
| **Goal-Based** | Acts to achieve a goal | Chess engine |
| **Utility-Based** | Maximizes a happiness score | Self-driving car |
| **Learning Agent** | Improves over time | ChatGPT |

```
┌─────────────────────────────────────┐
│              ENVIRONMENT            │
│                                     │
│   Sensors ──► [  AGENT  ] ──► Actuators
│                │       │            │
│                ▼       ▼            │
│           Perceive   Act            │
└─────────────────────────────────────┘
```

---

## 🗺️ State

A **state** is a complete description of the world at a particular moment in time. It captures everything the agent needs to know to make a decision.

> 💡 **Key Insight:** A state is like a **snapshot** — a photograph of the world frozen at one instant.

### Real-World Analogy
Think of a chessboard:
- Each unique arrangement of pieces = **one state**
- There are ~10⁴³ possible states in chess!

### Properties of a Good State Representation
- ✅ **Complete** — captures all relevant information
- ✅ **Compact** — no redundant data
- ✅ **Efficient** — easy to compare and store

```python
# Example: State in a simple grid world
state = {
    "agent_position": (2, 3),
    "goal_position":  (5, 7),
    "obstacles":      [(3, 3), (4, 5)],
    "steps_taken":    12
}
```

---

## 🚀 Initial State

The **initial state** is where the agent starts — the very beginning of the problem.

> 🎯 Every search problem begins with a single initial state. It's the root of the search tree.

### Why It Matters
- It defines the **starting point** of the problem
- All search paths originate from here
- The initial state is always **known** to the agent

### Example
```
Problem:  Navigate from City A to City B
Initial State:  Agent is at City A
Goal State:     Agent is at City B

        [A] ──── [B] ──── [C]
         ↑                 |
    Initial State       Goal State
```

---

## ⚡ Actions

**Actions** are the choices available to an agent in a given state. They are the "moves" the agent can make.

> 🎮 Think of actions like the buttons on a game controller — what can you press right now?

### Formal Definition
```
ACTIONS(s) → {a₁, a₂, a₃, ...}
```
Given a state `s`, returns the **set of all valid actions** the agent can take.

### Examples by Domain

| Domain | Possible Actions |
|---|---|
| **Grid World** | Move Up, Move Down, Move Left, Move Right |
| **Chess** | All legal piece moves from current position |
| **8-Puzzle** | Slide tile Up / Down / Left / Right |
| **Route Finding** | Drive to adjacent city |

```python
def actions(state):
    """Returns valid actions from the current state"""
    possible = []
    x, y = state["agent_position"]
    
    if x > 0:           possible.append("LEFT")
    if x < GRID_WIDTH:  possible.append("RIGHT")
    if y > 0:           possible.append("UP")
    if y < GRID_HEIGHT: possible.append("DOWN")
    
    return possible
```

---

## 🔄 Transition Model

The **transition model** (also called the **successor function**) defines what happens when an agent takes an action in a state. It answers: *"If I'm here and I do this — where do I end up?"*

### Formal Definition
```
RESULT(s, a) → s'
```
Given state `s` and action `a`, returns the **new state** `s'`.

> 🧭 The transition model is the "physics" of your problem world.

### Visualization
```
       Action: MOVE RIGHT
           │
  State s  │          State s'
  ┌─────┐  │          ┌─────┐
  │ 🤖  │──┼─────────►│  🤖 │
  │     │  │          │     │
  └─────┘             └─────┘
  (2, 3)               (3, 3)
```

### Code Example
```python
def result(state, action):
    """Returns the new state after applying action"""
    x, y = state["agent_position"]
    
    moves = {
        "UP":    (x, y - 1),
        "DOWN":  (x, y + 1),
        "LEFT":  (x - 1, y),
        "RIGHT": (x + 1, y)
    }
    
    new_pos = moves[action]
    return {**state, "agent_position": new_pos}
```

---

## 🌐 State Space

The **state space** is the collection of ALL possible states reachable from the initial state by any sequence of actions. It's the entire "universe" of the problem.

> 🌌 The state space is the map of everywhere the agent could possibly be.

### State Space Graph
```
                  [Initial]
                 /    |    \
               [A]   [B]   [C]
              /  \         |
           [D]   [E]      [F]
                  |
                [Goal]
```

### State Space Sizes (Real Examples)

| Problem | States |
|---|---|
| 8-Puzzle | 362,880 |
| 15-Puzzle | 1.3 trillion |
| Rubik's Cube | 4.3 × 10¹⁹ |
| Chess | ~10⁴³ |
| Go | ~10¹⁷⁰ |

> ⚠️ **Curse of Dimensionality:** State spaces grow exponentially — this is why smart search algorithms matter!

---

## 🎯 Goal Test

The **goal test** checks whether a given state is the **goal state** — i.e., has the agent solved the problem?

### Formal Definition
```
GOAL-TEST(s) → True / False
```

### Types of Goal Tests

**1. Single Goal State**
```python
def goal_test(state):
    return state["position"] == (5, 7)  # exact destination
```

**2. Set of Goal States**
```python
GOAL_STATES = {(5, 7), (5, 8), (6, 7)}

def goal_test(state):
    return state["position"] in GOAL_STATES
```

**3. Property-Based Goal**
```python
def goal_test(state):
    # Goal = any arrangement where all blocks are sorted
    return state["blocks"] == sorted(state["blocks"])
```

> 🏆 A problem can have **one goal**, **many goals**, or a **goal defined by a property** — not just a fixed position.

---

## 💰 Path Cost Function

The **path cost function** assigns a numerical cost to a sequence of actions (a path). It tells us *how expensive* it was to reach a state via a particular route.

> 💸 Not all paths are equal — we want the **cheapest** one, not just any solution!

### Formal Definition
```
g(n) = total cost from initial state to node n
```

Each action has a **step cost**: `c(s, a, s')`

```
g(path) = Σ c(sᵢ, aᵢ, sᵢ₊₁)
```

### Example: Route Finding

```
   A ──5──► B ──3──► D
   │                 ▲
   └────10───► C ────┘
               (4)

Path 1: A → B → D  =  5 + 3  = 8  ✅ Cheaper
Path 2: A → C → D  = 10 + 4  = 14
```

### Why It Matters in Algorithms

| Algorithm | Uses Path Cost? | Finds Optimal? |
|---|---|---|
| BFS | ❌ (uniform cost assumed) | ✅ (if uniform) |
| DFS | ❌ | ❌ |
| **UCS** | ✅ | ✅ |
| **A\*** | ✅ + heuristic | ✅ |

```python
def path_cost(path):
    """Calculate total cost of a path"""
    total = 0
    for i in range(len(path) - 1):
        s, a, s_next = path[i], actions[i], path[i+1]
        total += step_cost(s, a, s_next)
    return total
```

---

## 🧩 Putting It All Together

A **search problem** is formally defined as a tuple of all these components:

```
Problem = (Initial State, Actions, Transition Model, Goal Test, Path Cost)
```

### The Full Picture

```
┌──────────────────────────────────────────────────┐
│                  SEARCH PROBLEM                  │
│                                                  │
│  🚀 Initial State    →   Where we start          │
│  ⚡ Actions          →   What we can do          │
│  🔄 Transition Model →   Where actions take us   │
│  🌐 State Space      →   All reachable states    │
│  🎯 Goal Test        →   Are we done?            │
│  💰 Path Cost        →   How expensive is this?  │
│                                                  │
│  🤖 Agent uses ALL of the above to SEARCH for   │
│     the optimal path from Start → Goal           │
└──────────────────────────────────────────────────┘
```

---

## 📅 Daily Log

| Date | Topic | Status |
|---|---|---|
| Day 1 | Agent, State, Initial State, Actions, Transition Model, State Space, Goal Test, Path Cost | ✅ Done |
| Day 2 | *(coming soon)* | 🔜 |
| Day 3 | *(coming soon)* | 🔜 |

---

## 🛠️ Resources

- 📖 [Artificial Intelligence: A Modern Approach](https://aima.cs.berkeley.edu/) — Russell & Norvig
- 🎥 [CS50 AI — Harvard](https://cs50.harvard.edu/ai/)
- 🧮 [AI Search Visualizer](https://qiao.github.io/PathFinding.js/visual/)

---

<div align="center">

**⭐ Star this repo if it helps you learn!**

*"Every expert was once a beginner."*

Made with 💙 while learning AI from scratch

</div>
