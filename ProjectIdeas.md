### Potential projects

-  Run one of the MQPU examples on Perlmutter.

-  Interface the cudaq.observe call with the SLSQP optimizer from scipy.

-  Use the VQE H2 example from the docs and adapt it for LiH. Try different initializations as well as optimizers from other libraries. What parallelization technique can you use here?

-  Create an Ising Hamiltonian simulation. Try various scaling techniques.

-  Implement the QMCMC algorithm from this paper.
    https://arxiv.org/abs/2203.12497

-  Teleport a quantum state from Alice to Bob. 

-  Extend the hybrid qnns example to include a multi-qubit circuit, why not add the 8-qubit aca dataset to try improve accuracy with quantum and classical nodes? 

-  For the qnn example, code up a function to perform gradient descent by shifting parameters individually rather than simultaneously and compare the number of circuit evaluations as a function of epochs for both approaches. 

-  Demonstrate the barren plateau phenomena - show that as you increase qubit sizes, the variance in the gradient decreases. \
 https://arxiv.org/abs/1803.11173

-  Evaluate the implementation of the quantum natural gradient optimization technique to accelerate VQE convergence. \
  https://arxiv.org/abs/1909.02108

-  Using a parameterized quantum circuit and a neural network, create a hybrid quantum GAN. 
https://arxiv.org/abs/2212.11614 \
https://pennylane.ai/qml/demos/tutorial_QGAN.html \
https://pennylane.ai/qml/demos/tutorial_quantum_gans.html 

-  Circuit cutting is a powerful tool for running large quantum circuits on small computers. Implement this in CUDA Quantum. \
https://arxiv.org/abs/2012.02333

-  Modify the qnn example, use a sci-kit learn dataset and implement the data reupload technique. 
https://arxiv.org/abs/1907.02085 

-  Classical shadows is a powerful technique to reconstruct quantum states by measuring the circuit in random bases and postprocessing the data. Implement this in CUDA Quantum. 
https://arxiv.org/abs/2002.08953 \
https://pennylane.ai/qml/demos/tutorial_classical_shadows.html 



