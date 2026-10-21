# Radar Signal Processing: Multi-Baseline FMCW Radar Positioning: Unified Optimization Framework and Algorithm Design [with Python Code]

1. Framework design background and core challenges multi-base frequency modulated continuous wave (FMCW) radar positioning system through distributed site collaborative perception of target location, The core contradiction lies in the conflict between the heterogeneity of distributed measurements and the global consistency of positioning results. Specific manifestation:
Site heterogeneity: Hardware characteristics of different radar sites (e.g. Frequency offset, Noise level, The antenna gain) has inherent differences. Causes the fidelity of the original echo signal to be inconsistent. Directly affects the reliability of single-site independent estimation.
Calculating the Balance between Constraints and Efficiency: Distributed systems need to strike a balance between positioning accuracy and computational overhead. While centralized optimization can ensure global optimality, However, it is necessary to transmit a large amount of raw data. Difficult to adapt to the real-time requirements of edge computing scenarios.
Multi-target and clutter interference: In complex scenes, there are multi-target overlapping and environmental clutter. It is easy to cause false target detection. Traditional methods are difficult to suppress redundant solutions. Sparseness and interpretability of solutions need to be enhanced through prior constraints.
Geometric Consistency Deficit: The estimated distance/angle based on local measurements at various sites may be distorted due to synchronization errors (such as time offset, phase deviation), leading to inconsistencies across site results and necessitating geometric correction.
The core goal of the unified optimization framework is to achieve robust, efficient, and consistent target positioning in complex scenarios by integrating the aforementioned constraints through weighted multi-objective function fusion.
II. Unified objective function design, the objective function is designed by integrating data fidelity. Sparseness and cross-site consistency constraints. Achieving global optimal estimation. Mathematics is defined as:
Among them, the position set of each target (three-dimensional coordinates) is used to balance the weights of various constraints.
2.1 Physical meaning of data fidelity item: Measuring the degree of matching between estimated signals and actual measured signals. Ensure that the positioning results are consistent with the original observation data. It is the core constraint of the objective function.
Mathematical form: For a radar site, The measured backscatter signal at the next site is (frequency domain matrix, Dimensions are (distance dimension × fast time dimension). The mapping function represents the theoretical echo signal generated based on the target position. Data fidelity item is defined as:
The Frobenius norm is used to quantify the global difference between the measured and theoretical signals. This constraint forces the estimated position to generate signals that are as close as possible to the true observations, ensuring data consistency in the positioning results.
2.2 Sparseness term physical meaning: Utilizing prior knowledge that the number of targets is much less than possible positions in space. Suppress redundant targets caused by noise or clutter through regularization. Ensure sparsity of the solution (i.e., only keep the true target).
Mathematical Form: L1 regularization (sparsity constraint on the target position vector):
L1 regularization is more likely to produce sparse solutions compared to L2 regularization. Can effectively eliminate "false targets" whose magnitude is close to zero. Enhance the interpretability of positioning results. Weight control sparsity strength: The larger the value, The more sparse the decomposition is (the stronger the ability to suppress redundancy), However, it may excessively eliminate weak targets; The smaller the value, The higher the tolerance for noise, Easy to retain false targets.
2.3 Physical Meaning of Cross-Site Consistency Items: Ensure that measurements on different sites for the same target meet geometric constraints (such as distance differences). Angle relationship), Correct for cross-site conflicts caused by synchronization errors or hardware deviations.
Mathematical form: Let the position of the ith station be The distance from the target to the station is respectively, ,  The pre-calibrated reference distance deviation is (determined by the baseline distance between stations and synchronization parameters). Consistency item is defined as:
This constraint enforces the consistency of cross-site distance estimates, forcing each site to measure the same target in geometric compatibility, enhancing the system's global consistency.
3. Mapping function design: The mapping function from target position to signal feature is the core component connecting target position and echo signal. Responsible for converting spatial positions into frequency domain/time domain features that can be compared with actual measured signals. Based on the physical propagation model of FMCW signal. Design two mapping schemes:
3.1 Mask-based Mapping Physical Principle: Leveraging the linear correlation between FMCW signal "distance-frequency" (the target distance is directly proportional to the frequency offset of the echo), applying Gaussian soft-thresholding in the frequency domain to the target's frequency range, highlighting the effective signal components and suppressing noise and clutter.
Mathematical Formulation: For the target, its distance to the destination corresponds to a frequency (starting frequency, speed of light, round-trip time delay of the signal). The Gaussian mask is defined as follows:
where is the element-wise product, and is the frequency resolution parameter (controls the mask width). This mapping has a clear physical meaning, low computational complexity, and is suitable for scenarios with strong noise.
Atom-based Mapping Physical Principle: Based on the sinusoidal modulation characteristics of FMCW signals, the backscatter signal is fitted by linear combinations of sinusoidal atoms (including frequency, phase, amplitude parameters), closer to the physical model of signal propagation (amplitude attenuation, phase delay relationship with distance).
Mathematical Formulation: Sinusoidal atoms are defined as follows: where is the amplitude weight (related to the target reflectivity and distance attenuation), is the fast time sampling points, and is the position-dependent phase (proportional to distance). The mapping function is:
By solving the least squares fitting to obtain weights, the atom combination is made as close as possible to the measured signal. This mapping provides a more detailed depiction of signal details, suitable for high signal-to-noise ratio scenarios, but has higher computational complexity than mask-based methods.
Solving Algorithm Design: To balance convergence speed and stability against non-smoothness (L1 regularization) and nonlinearity (geometric constraints), two solving algorithms are designed to address these challenges:
4.1 Proximal Gradient (PG) Algorithm Applicable Scenarios: Optimization problems with non-smooth sparse terms, theoretically stable convergence, suitable for scenarios requiring high robustness.
Iterative Principle: Through gradient descent updates data fidelity term and consistency term, then through proximal operator handles sparsity constraint, iterative formula is:
where is the step size (adjusted adaptively through backtracking method to balance convergence speed and stability), represents the gradient operator, and is the proximal operator (soft threshold operation corresponding to L1 regularization: ).
Characteristics: Each iteration has low complexity, is not sensitive to initial values, but converges slowly (linear convergence).
4.2 GN/LM (Gauss-Newton/Levenberg-Marquardt) Algorithm Applicable Scenarios: Suitable for smooth objective functions (can ignore sparseness constraints or use smooth approximations), accelerate convergence through second-order approximation, suitable for scenarios with high real-time requirements.
Iterative Principle: Use the Jacobian matrix to approximate the Hessian matrix, balance convergence stability and step size with damping coefficients:
The target function's Jacobian matrix is the one concerning, the residual vector (expansion of) is the one, and the damping coefficient (increases to degenerate into gradient descent to ensure stability; decreases to approach Gaussian-Newton method, accelerating convergence).
Characteristics: Fast convergence speed (superlinear convergence), but sensitive to initial values, requires line search to ensure the target function descends.
Fifth, Unified Optimization Framework Through Configurable Weight Coefficients, Mapping Functions, and Solving Algorithms, Achieves Compatibility with Different Scenarios:
Low Signal-to-Noise Ratio Scenarios: Adopt mask-based mapping (strong noise resistance) + PG algorithm (high robustness), by increasing to suppress false targets caused by noise.
High Real-Time Requirement Scenarios: Adopt atom-based mapping (high signal fitting accuracy) + GN/LM algorithm (fast convergence), by reducing to lower computational overhead (suitable for scenarios with good site synchronization).
Multi-Objective Dense Scenarios: Increase and strengthen sparsity and geometric constraints, avoiding overlapping target positions and cross-site contradictions.
In summary, this framework integrates data-driven constraints with physical priors through modular design, providing a flexible and robust solution for multi-base FMCW radar positioning.
Sixth, Simulation Results

## Images

![(Radar Signal Processing)Multi-Base Station FMCW Radar Positioning: Unified Optimization Framework and Algorithm Design [with Python code]](01.jpg)
![(Radar Signal Processing)Multi-Base Station FMCW Radar Positioning: Unified Optimization Framework and Algorithm Design [with Python code]](02.jpg)
![(Radar Signal Processing)Multi-Base Station FMCW Radar Positioning: Unified Optimization Framework and Algorithm Design [with Python code]](03.jpg)
![(Radar Signal Processing)Multi-Base Station FMCW Radar Positioning: Unified Optimization Framework and Algorithm Design [with Python code]](04.jpg)
![(Radar Signal Processing)Multi-Base Station FMCW Radar Positioning: Unified Optimization Framework and Algorithm Design [with Python code]](05.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
