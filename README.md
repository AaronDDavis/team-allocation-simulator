# Team Allocation Simulator

A constraint satisfaction algorithm that partitions 6,000+ students across multiple tutorial groups into balanced teams of 4–10 members, subject to competing statistical and diversity constraints.

## Table of Contents

- [Team Allocation Simulator](#team-allocation-simulator)
- [Problem Statement](#-problem-statement)
- [Algorithm Overview](#-algorithm-overview)
- [Constraint Satisfaction Logic](#-constraint-satisfaction-logic)
  - [Constraint Definitions](#constraint-definitions)
  - [Violation Detection](#violation-detection)
  - [Balancing Competing Constraints](#balancing-competing-constraints)
- [Dynamic Constraint Relaxation](#-dynamic-constraint-relaxation)
- [Execution Flow](#-execution-flow)
- [How to Run](#-how-to-run)
- [Technical Details](#-technical-details)
- [Allocation Quality](#-allocation-quality)
- [🧾 License](#-license)
- [📬 Contact](#-contact)

---

## 🎯 Problem Statement

Given a cohort of students distributed across tutorial groups, allocate them into teams that simultaneously satisfy:

- **Gender balance**: Each team's gender ratio approximates the tutorial-wide ratio
- **CGPA uniformity**: Each team's average CGPA remains within a narrow band of the tutorial mean
- **School diversity**: Each team contains students from multiple schools to prevent institutional clustering

These constraints frequently conflict—optimizing for one degrades another. The challenge is to find a feasible allocation at scale without exhaustive enumeration.

---

## 🧠 Algorithm Overview

The solution employs a **randomized iterative allocation strategy** with adaptive constraint relaxation:

1. **Random sampling** generates candidate teams by stochastically selecting students according to target gender distributions
2. **Constraint validation** evaluates each team against CGPA and diversity thresholds
3. **Rejection and retry** discards invalid teams and regenerates from the remaining pool
4. **Dynamic relaxation** progressively loosens constraints when repeated failures indicate infeasibility under current thresholds

This approach is appropriate because:

- The solution space is combinatorially large (50 students into groups yields ~10^15 configurations for size-6 teams)
- Constraints are statistical rather than deterministic—near-optimal is acceptable
- Randomization explores the space efficiently without requiring a formal objective function
- Adaptive relaxation guarantees termination without manual threshold tuning

---

## ⚙️ Constraint Satisfaction Logic

### Constraint Definitions

**Gender Distribution**  
For a tutorial with `M` males and `F` females across `k` groups of size `[s₁, s₂, ..., sₖ]`:

<<<<<<< Updated upstream
The program will then process all 120 tutorial groups and print the final team rosters for each one.
=======
```
males_in_group_i = round((M / (M + F)) × sᵢ)
females_in_group_i = sᵢ - males_in_group_i
```

This proportional allocation maintains the tutorial-wide gender ratio in each team.

**CGPA Tolerance**  
Each team's average CGPA must satisfy:

```
|avg_cgpa_team - avg_cgpa_tutorial| ≤ ε
```

where `ε` begins at 0.005 and increases adaptively.

**School Diversity**  
Each team must contain at least `d` unique schools, where `d` initially equals team size and decreases adaptively.

### Violation Detection

After random student selection:

1. Compute team average CGPA
2. Count unique schools represented
3. Reject team if either constraint fails
4. Return students to pool and retry

### Balancing Competing Constraints

When constraints conflict persistently:

- **Primary lever**: Widen CGPA tolerance (`ε += 0.005` every 250,000 iterations)
- **Secondary lever**: Reduce minimum school diversity when CGPA tolerance exceeds 0.015–0.025
- **Tertiary lever**: Adjust gender distribution by ±1 student in select groups (rare, only after extreme iteration counts)

This hierarchy reflects constraint rigidity: gender ratios are institutional requirements, CGPA uniformity prevents skill imbalance, and school diversity is desirable but flexible.

---

## 🔄 Dynamic Constraint Relaxation

The algorithm detects convergence failure through iteration counters:

```
j: single group failures (→ reset students and retry)
k: tutorial-level failures (→ widen CGPA tolerance or reduce diversity requirement)
l: extreme failure (→ adjust gender distribution as last resort)
```

**Example progression** (assuming tutorial struggles to satisfy constraints):

- Iterations 0–250K: `ε = 0.005`, `d = team_size`
- Iterations 250K–500K: `ε = 0.010`, `d = team_size`
- Iterations 500K–750K: `ε = 0.015`, `d = team_size - 1`
- And so on...

This guarantees feasibility because constraints relax monotonically until the solution space contains valid allocations. The algorithm never fails—it converges to the tightest feasible constraints for the given input.

---

## ▶️ Execution Flow

```
1. Parse CSV → organize students by tutorial group
2. Prompt user for desired team size (4–10)
3. Compute group size distribution to handle remainders
4. For each tutorial:
   a. Separate students by gender
   b. Calculate proportional gender targets per team
   c. Loop:
      i.   Randomly sample students according to gender targets
      ii.  Validate CGPA and diversity constraints
      iii. If valid → commit team; else → reject and retry
      iv.  If repeated failures → relax constraints adaptively
   d. Merge remaining students into final team (with validation)
5. Output teams to console
```

---

## 📋 How to Run

**Input**: CSV file `records.csv` with columns:

```
Student ID, Name, Gender, School, CGPA, Tutorial Group
```

**Execution**:

```bash
python TeamAllocationSimulator.py
```

**Prompt**: Enter team size (4–10). The script distributes remainders optimally (e.g., size 8 with 50 students → 4 teams of 8, 2 teams of 9).

**Output**: For each tutorial group, prints teams with student details (ID, name, gender, school, CGPA).

---

## 🔧 Technical Details

- **Language**: Python 3.x
- **Dependencies**: Standard library only (`csv`, `random`)
- **Scale**: Tested on cohorts of 6,000+ students across 120+ tutorial groups
- **Performance**: Typically converges within seconds; worst-case scenarios (highly constrained tutorials) may take 1–2 minutes

## 📊 Allocation Quality

The algorithm prioritizes:

1. **Feasibility** (always finds a solution)
2. **Gender equity** (strict enforcement of proportional ratios)
3. **Academic balance** (CGPA spread typically ≤0.01 from tutorial mean)
4. **Institutional diversity** (maximizes unique schools per team within feasibility constraints)

Final allocations reflect the tightest achievable thresholds for each tutorial's student composition.

---

## 🧾 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

You’re welcome to use, modify, or build upon this project for learning or non-commercial purposes.

---

## 📬 Contact

Developed by **Aaron Davis**

Email: [aaronddavis001@gmail.com]

LinkedIn: [https://linkedin.com/in/aaron-daniel-davis]
>>>>>>> Stashed changes
