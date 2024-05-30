# Slip Dirichlet Boundary Conditions

Although they might be satisfied at the start of a time step, general slip Dirichlet boundary conditions (DBC) present unique challenges. Unlike the sticky DBCs, they cannot be directly addressed using the [DOF elimination method](lec5.2-DOF_elimin.md), primarily because their constraint Jacobian does not consist of identity matrix blocks. To navigate this complexity, we can adopt a change-of-basis strategy.

Before delving into the more general scenarios, it's insightful to first examine a particular type of slip DBC: those that are axis-aligned. Understanding this specific case will lay the groundwork for tackling the broader range of slip DBCs.
