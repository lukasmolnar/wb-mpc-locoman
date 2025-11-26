# Whole-Body MPC for Loco-Manipulation

Official code for the paper: **Whole-Body Inverse Dynamics MPC for Legged Loco-Manipulation**, IEEE Robotics and Automation Letters (RA-L) 2025. Lukas Molnar, Jin Cheng, Gabriele Fadini, Dongho Kang, Fatemeh Zargarbashi, Stelian Coros. *ETH Zurich*

[<u>Paper</u>](https://ieeexplore.ieee.org/document/11266934) | [<u>arXiv</u>](https://arxiv.org/abs/2511.19709) | [<u>Video</u>](https://www.youtube.com/watch?v=glWWE-754mI&t=16s) | [<u>Website</u>](https://lukasmolnar.github.io/wb-mpc-locoman/)

<p float="left">
  <img src="utils/media/b2_z1_tracking.gif" width="49%" />
  <img src="utils/media/b2_z1_pulling.gif" width="49%" />
</p>

## Installation

Create conda environment:

```bash
conda create -f environment.yaml
conda activate wb-mpc
```

## Usage

Run the main script:

```bash
python main.py
```

Within the script, the following parameters are defined:
- Robot: Model and dynamics (whole-body or centroidal, see table below)
- Targets: Base velocity, and arm end-effector velocity/force
- OCP: Number of nodes and time discretization
- Gait: Type, period and swing parameters
- Solver: Type ("fatrop", "ipopt", or "osqp"), warm-starting, code-compilation

## Optimal Control Problem

### Dynamics

The table below shows the available dynamics models (whole-body and centroidal variants). See the paper for detailed benchmarking results.

For certain models, the argument `include_base` determines whether the base variable is part of the input (set in `args.py`). If it is included, the dynamics are ensured through a path constraint on each node. If it is not included, the base dynamics propagate through the state transition function.

![alt text](utils/media/dynamics_models.png)

### Parameters

The optimization parameters fall into the following categories:
- Initial state: `x_init`
- Tracking targets: `base_vel_des`, `arm_vel_des`, `arm_force_des`
- Gait schedule: `contact_schedule` (0 or 1), `swing_schedule` (phase between 0 and 1)
- Tunable parameters:
    - `Q_diag`, `R_diag`: Diagonals of the weight matrices
    - `swing_period`, `swing_height`, `swing_vel_limits`: Swing trajectory params
    - `dt_min`, `dt_max`: Initial and final time step sizes of the geometric series
    - `n_contacts`: Number of stance feet (eg. 2 for trot)

## Solvers

### Interior-Point: Fatrop and Ipopt

The interior-point solvers **Fatrop** and **Ipopt** are supported, which directly solve the constrained nonlinear optimization problem until convergence.

As described in the paper, **Fatrop** exploits the block-sparse structure of stage-wise constriants, and shows a >10x speedup over **Ipopt** (higher speedup for longer horizons). The solver is warm-started with the MPC solution from the previous iteration. 

### Sequential Quadratic Programming: OSQP

Instead of solving the full NLP, it is converted to a Sequential Quadruatic Program (SQP). Each SQP iteration is solved with OSQP, and the solution is updated using the Armijo line-search method.


### Code-generation

Currently code-generation is only supported for **Fatrop**, since it showed the most promising results in terms of solve-time and convergence. See the `/codegen` folder for how to generate C code for the solver and compile it to a shared library.

For hardware deployment the shared library can be loaded with `casadi::external` in C++. This allows for straight forward deployment without having to reformulate the optimization problem in C++. It also allows for real-time parameter tuning.

## Citation

If you use this code in your research, please cite our paper:
```bibtex
@article{wb-mpc-locoman,
  author={Molnar, Lukas and Cheng, Jin and Fadini, Gabriele and Kang, Dongho and Zargarbashi, Fatemeh and Coros, Stelian},
  journal={IEEE Robotics and Automation Letters}, 
  title={Whole-Body Inverse Dynamics MPC for Legged Loco-Manipulation}, 
  year={2025},
  volume={},
  number={},
  pages={1-8},
  keywords={Dynamics;Robots;Robot kinematics;Manipulator dynamics;Legged locomotion;Force;Quadrupedal robots;Foot;Real-time systems;Planning;Legged Robots;Mobile Manipulation;Whole-Body Motion Planning and Control},
  doi={10.1109/LRA.2025.3636005}}
```

## License

This codebase is under [CC BY-NC 4.0 license](https://creativecommons.org/licenses/by-nc/4.0/deed.en). You may not use the material for commercial purposes, e.g., to make demos to advertise your commercial products.


## Contact

Feel free to open an issue or discussion if you encounter any problems or have questions about this project.

For collaborations, feedback, or further inquiries, please reach out to:

- Lukas Molnar: [lukas.molnar@bluewin.ch](mailto:lukas.molnar@bluewin.ch).

We welcome contributions and are happy to support the community in building upon this work!