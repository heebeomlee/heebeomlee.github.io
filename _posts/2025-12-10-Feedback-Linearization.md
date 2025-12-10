---
layout: single
title: "Feedback Linearization of Nonlinear Systems"
---
> Reference : https://www.youtube.com/playlist?list=PL9s6YpaXIcJvitrw3aOOeNKycTrr3QBGW 

### Preliminary
* Lie Derviative

### Feedback Linearization
* Powerful technique to design controllers for *nonlinear* systems
* Unlike nonlinear control theory, linear control theory is well developed with systematic approaches  
 → <U>Goal: somehow obtain linear representation of a nonlinear system</U>
    - *Jacobian Linearization* approximates a nonlinear function to a linear function  
    → only valid near equilibrium point (higher order terms are neglected)
    - *Feedback linearization* is an <U>exact linearization</U> of a nonlinear system  
    → Different state space representation using new state variable  
    → No approximations

---

#### Motivation ####
* Pendulum Example
    1. Design control input **u** using newly defined variable **v** such that the nonlinearities are canceled out
    2. Transformation of nonlinear system into *linear system* with new control input **v** (exact representation)
    3. Apply linear control theory (e.g. state feedback control, pole placement technique)
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-10-Feedback-Linearization/image.png" style="width:55%;" />
</div>

#### <U>Q. Is it always possible to obtain FB linearization?</U> ####
* If the nonlinear system is represented by the following particular structure → **YES**
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-10-Feedback-Linearization/Image 1.png" style="width:20%;" />
</div>

* By taking the following control law, we can utilize any standard contol technique for linear system (e.g. state feedback control law), where A and B must be *controllable*
    - Hurwitz : matrix where all eigen values are in the left half s-plane  
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-10-Feedback-Linearization/Image 2.png" style="width:25%;" />
</div>
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-10-Feedback-Linearization/Image 3.png" style="width:60%;" />
</div>

#### <U>Q. What if the system is not in that form?</U> ####
→ Define a *State Transformation* to bring the system in the desired form
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-10-Feedback-Linearization/Image 4.png" style="width:55%;" />
</div>

#### <U>Q. Does there always exist such a transform?</U> ####
#### <U>Q. If such a transformation exists, then how to find it?</U> ####

#### Full state linearization and input-output linearization? #### 
→ *Stabiliztion problem* : Design control law such that all the **state variables** converge to the equilibrium point  
→ *Tracking problem* : Design control law such that the controlled **output** tracks a reference signal
<br>
<div style="text-align:center;">
    <img src="../images/2025-12-10-Feedback-Linearization/Image 6.png" style="width:55%;" />
</div>

1. In full state linearization, *State equations* of the transformed system are linear  
2. By transformation, although we have achieved full state linearization, input-output relation is not *linear*
3. Tracking problem is difficult to solve because input-output has nonlinear relationship. By taking a new control input, we can linearize input-output relationship even though there are nonlinearities in the state equations  
4. In input-ouput linearization, *Output equation* is included  
5. We have to ensure that the state variable is well-behavied, unbounded → stability of internal dynamics (internal : x1 has no influence of the output)  

---



