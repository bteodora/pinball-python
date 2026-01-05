<div align="center">
  <img src="Untitled.png" alt="Engine Logo" width="300"/>
  <br>

  <h1>Space Pinball: Computational Physics & Kinematics Engine</h1>
  <h3>Discrete-Time Rigid Body Dynamics & SAT Collision System</h3>

  <p>
    <img src="https://img.shields.io/badge/Language-Python_3.11-3776ab?style=for-the-badge&logo=python&logoColor=white" alt="Python"/>
    <img src="https://img.shields.io/badge/Rendering-Pygame_Surface_Engine-green?style=for-the-badge" alt="Renderer"/>
    <img src="https://img.shields.io/badge/Mathematics-Vector_Calculus-orange?style=for-the-badge" alt="Math"/>
    <img src="https://img.shields.io/badge/Type-Physics_Engine_Wrapper-red?style=for-the-badge" alt="Type"/>
  </p>

  <p>
    <strong>A custom-built physics engine implementing Newtonian mechanics, vector reflection algorithms, and Separating Axis Theorem (SAT), wrapped within a gamified Pinball interface.</strong>
  </p>

  <p>
    <a href="#theoretical-framework"><strong>Theoretical Framework</strong></a> •
    <a href="#simulation-modules"><strong>Simulation Modules</strong></a> •
    <a href="#system-architecture"><strong>System Architecture</strong></a>
  </p>
</div>

---

## 1. Project Abstract

**Space Pinball** is a comprehensive study in computational physics, designed to simulate the behavior of rigid bodies in a 2D environment without reliance on external physics middleware (such as Box2D).

**It is important to note that "Space Pinball" is merely the graphical wrapper for the underlying engine.** The core objective of this engineering effort was to mathematically model the conservation of momentum, gravitational decomposition on inclined planes, and complex collision resolution. The arcade elements serve only as a visual testbed to validate the accuracy of the physical kinematic equations in real-time.

---

## 2. Theoretical Framework & Mathematical Model

The engine's robustness stems from its adherence to fundamental kinematic equations and vector algebra. The following models govern the simulation state.

### 2.1. Vectorial Kinematics & Euler Integration
The simulation utilizes **Semi-Implicit Euler Integration** to solve the equations of motion numerically. Unlike standard Euler, this method updates velocity before position, preserving energy more effectively in harmonic systems.

The state update for any given frame $dt$ (delta time) is defined as:

$$ \vec{v}_{t+1} = \vec{v}_t + \frac{\vec{F}}{m} \cdot dt $$
$$ \vec{p}_{t+1} = \vec{p}_t + \vec{v}_{t+1} \cdot dt $$

Where:
*   $\vec{v}$ is the velocity vector.
*   $\vec{p}$ is the position vector.
*   $\vec{F}$ is the net force vector (sum of Gravity, Friction, and Normal forces).

### 2.2. Gravitational Decomposition
The simulation models a pinball table tilted at an angle $\theta = 30^\circ$. Gravity is not applied as a scalar $9.81 m/s^2$, but decomposed into vector components relative to the board's surface.

The effective acceleration due to gravity $\vec{a}_g$ is calculated as:

$$ \vec{a}_g = g \cdot \sin(\theta) \cdot \hat{j} $$

This results in a realistic acceleration curve that mimics the sliding motion of a ball on a focused incline rather than free-fall.

### 2.3. Impulse-Momentum Collision Resolution
Collision response is handled via vector reflection. When a dynamic body (Ball) intersects with a static body (Wall/Obstacle), the engine calculates the reflection vector $\vec{R}$ based on the incident vector $\vec{I}$ and the surface normal $\vec{N}$.

$$ \vec{R} = \vec{I} - (1 + e)(\vec{I} \cdot \vec{N})\vec{N} $$

*   $e$ represents the **Coefficient of Restitution** (elasticity).
*   If $e = 1.0$, the collision is perfectly elastic (no energy loss).
*   If $e < 1.0$, kinetic energy is dissipated, simulating realistic surface friction and heat loss.

### 2.4. Separating Axis Theorem (SAT)
For complex interactions between the ball and convex polygons (such as the rotating flippers or trapezoidal bumpers), simple bounding box checks are insufficient.

The engine implements **SAT**, which asserts that two convex shapes are not colliding if and only if there exists a single axis onto which their projections do not overlap. The algorithm iterates through all edge normals of both polygons:

1.  **Project** vertices of Polygon A and Polygon B onto the normal axis $\vec{n}$.
2.  **Calculate** Min/Max scalar values for both projections.
3.  **Check Overlap:** If $Max_A < Min_B$ or $Max_B < Min_A$, a "Separating Axis" exists, and collision is impossible.

This ensures mathematical precision even when the flippers are rotating at high angular velocities.

---

## 3. Simulation Modules (Project Structure)

The repository is organized into distinct modules representing the iterative development of the physics engine. A centralized dispatcher (`meni.py`) is provided to launch specific simulation environments.

### Core Execution
*   **`meni.py` (Dispatcher)**
    *   **Role:** The Command Line Interface (CLI) / GUI entry point.
    *   **Function:** Allows the user to select and execute any of the simulation stages listed below without modifying source code.

### Evolutionary Stages

#### Phase I: Fundamental Forces
*   **`gravitacija.py` (Gravity Prototype)**
    *   **Focus:** Vertical kinematics.
    *   **Description:** An isolated test environment for calibrating the gravitational constant and pixel-to-meter conversion ratios. Validates the acceleration formulas before integration into the main loop.

#### Phase II: Elastic Dynamics
*   **`Prvobitno_odbijanje.py` (Reflection Prototype)**
    *   **Focus:** Collision vectors.
    *   **Description:** A zero-gravity environment designed to test the vector reflection formula $\vec{R} = \vec{I} - 2(\vec{I} \cdot \vec{N})\vec{N}$. Used to debug normal vector calculations on arbitrary lines.

#### Phase III: Integration
*   **`Medjukorak_flipera.py` (Integration Build)**
    *   **Focus:** System composition.
    *   **Description:** The first successful merge of gravitational logic and collision logic. Represents the alpha build of the pinball mechanics.

#### Phase IV: Production Release
*   **`flipper.py` (Stable Engine v1.0)**
    *   **Focus:** The complete Space Pinball experience.
    *   **Features:** Full SAT implementation, dynamic sprite rotation, score tracking, UI scaling, and polished asset rendering. This is the primary executable.

#### Phase V: Experimental / R&D
*   **`flipper2.2.py` (Multi-Body Beta)**
    *   **Focus:** N-Body interaction.
    *   **Status:** Experimental.
    *   **Description:** An advanced branch attempting to simulate multiple dynamic bodies simultaneously.
    *   *Engineering Note:* This module highlights the complexities of "Continuous Collision Detection" (CCD). When three bodies (Wall + Ball A + Ball B) collide in the same frame, constraint solving becomes non-trivial. This file serves as a testbed for future multi-threaded collision solvers.

---

## 4. System Architecture

The application is architected around a high-performance **Game Loop** that decouples input handling, physics updates, and rendering.

### 4.1. The Entity-Component Hierarchy
The codebase utilizes strict Object-Oriented Programming (OOP) to define simulation entities:

*   **`Poly` Class:** The base geometric primitive. Handles vertex storage and SAT projection logic.
*   **`Flipper` (extends `Poly`):** Implements dynamic topology. It applies 2D Rotation Matrices to its vertices in real-time based on user input.
*   **`Ball` (extends `Circle`):** The primary physics agent. It encapsulates mass, velocity, acceleration, and the integration state.

### 4.2. Algorithmic Flow
1.  **Input Polling:** Asynchronous capture of keyboard interrupts.
2.  **State Integration:** `Ball.update()` calculates the new theoretical position based on current velocity and $\Delta t$.
3.  **Broad Phase Detection:** Spatial checks eliminate objects too far to collide.
4.  **Narrow Phase Resolution:** SAT and Vector Projection determine exact collision manifolds.
5.  **Velocity Correction:** Vectors are reflected, and positions are nudged to prevent mesh interpenetration.
6.  **Rasterization:** The scene is drawn to the Pygame surface.

---

## 5. Installation & Execution

### Prerequisites
*   **Python 3.11+** (Required for updated math libraries).
*   **Tkinter** (For display resolution polling).

### Setup

1.  **Clone the Repository**
    ```bash
    git clone https://github.com/bteodora/pinball-python.git
    ```

2.  **Install Dependencies**
    The engine relies on `pygame` for hardware abstraction.
    ```bash
    pip install pygame
    ```

3.  **Launch the Dispatcher**
    To access the main menu and select a simulation module:
    ```bash
    python meni.py
    ```

---

## 6. Future Engineering Roadmap

The "Experimental" branch (`flipper2.2.py`) has identified key areas for the next generation of the engine:

1.  **Spatial Partitioning (Quadtrees):** To optimize collision detection from $O(n^2)$ to $O(n \log n)$ for multi-ball scenarios.
2.  **Sub-stepping:** Dividing the frame time $dt$ into smaller chunks to solve the tunneling effect observed in high-velocity collisions in the experimental branch.
3.  **Constraint Solving:** Implementing a sequential impulse solver to handle the "stacking" issues found in multi-body simulations.

---

**Technical Disclaimer:**
While presented as a "game," this codebase is fundamentally a **Physics Engine Demo**. The graphical interface, score system, and pinball theme are abstraction layers built on top of the core physics kernel. The primary scientific contribution of this repository is the implementation of the `Poly` collision logic and the `Ball` vector kinematics from scratch, demonstrating a mastery of computational geometry and rigid body dynamics.
