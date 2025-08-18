# CLIMU-3

<font size="5"><b>C</b>ontinually <b>L</b>earning <b>I</b>nertial <b>M</b>easurement <b>U</b>nit - Trimodal</font>

In real-time systems such as self-driving vehicles, drones, industrial robots etc. Inertial Measurement Units (IMUs) play a critical role in data acquisition through accelerometer (accel), gyroscope (gyro), and magnetometer (mag), to give precise trajectory mapping. However, the data acquired must be digitally filtered to cancel out internal and externally induced noise. This filtering process needs to be accurate and fast, plus account for multi-modal efficiency. Hence, it is crucial to design a filter architecture that is accurate, fast, and works on partial modalities in real-life scenarios.



<p align="center">
  <img width="872" height="509" alt="image" src="https://github.com/user-attachments/assets/ba7c26a4-ad41-44e8-b20a-d4efb308c115" />
  <br>
  
  <font size="5"><strong>CLIMU-3 Architecture</strong></font>
</p>



● The edge consists of a trimodal IMU feeding sampled digital data to a Microcontroller Unit (MCU), on which the filters are programmed in the L1 block.

● The ANN-based Differentiable filter and the Adaptive FIR filters are placed in cascade and are updated using micro-Stochastic Gradient Descent (μSGD) and Least Mean Squares (LMS) algorithms, respectively.

● The local server consists of 
1. L3, a frozen Deep Neural Network (DNN) trained on IMU datasets to filter raw data, and
2. L2, a Low-Rank Adaptor (LoRA) neural network, which serves the purpose of bridging the parameter gap between L1 and L3, and fine-tuning itself to new data.
L2 with relatively lower softmax temperature also helps provide sharper ground truth/reference signal to the LMS-FIR filter, compared to L3 by itself.

● Knowledge distills as L3 → L2 → L1, thus fitting a percentage of performance of larger models into smaller models, which are edge-deployable. The backpropagation algorithms are made possible by the cross-entropy distillation losses L1-L2 and L2-L3, with L1-L2 also posing as the error signal of the LMS-FIR filter updating its taps using Continual Learning (CL).

● Training L2 on full modality, i.e. accel, gyro, and mag, and distilling its knowledge into L1, allows L1 to relatively accurately filter the IMU data on partial modality during inference, ex: accel, and gyro only; as mag can often dysfunction due to Electromagnetic Interference (EMI) in real-life use cases. This approach helps overcome the cost that comes with Generative Imputation techniques to address missing modalities; higher time, computation, and energy requirements for real-time edge embedded systems.
