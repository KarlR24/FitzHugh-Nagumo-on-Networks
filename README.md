# FitzHugh-Nagumo dynamics on Networks
Python implementation of local FitzHugh-Nagumo dynamics on given networks with in Jupyter notebooks.
Every node of the network is a FitzHugh-Nagumo (FHN) neuron which is coupled to its nearest neighbors. \
Here, the ODE is approximated according to the [Euler method](https://en.wikipedia.org/wiki/Euler_method). \
The class is built according to [fabridamicelli's Kuramoto repository](https://github.com/fabridamicelli/kuramoto) and rewritten for FitzHugh-Nagumo dynamics. 

This repository contains:
 - a class which computes the time evolution of the local FitzHugh-Nagumo dynamics for given parameter values and network
 - different visualizations of the dynamics 

FitzHugh-Nagumo dynamics for node i: \
$\dot{v_i} = v_i - \frac{v_i^3}{3} - w_i + \frac{k}{N}* \sum_{j =1,...,N} a_{ij} (v_i - v_j)$ \
$\dot{w_i} = \frac{1}{\tau} (v_i - bw_i + a)$ \
where $a_{ij}$ denotes the entries of the adjacency matrix (for a given network) and $N$ is the number of nodes. \
Remark: Depending on the choice of parameters in the model the behaviour change qualitatively (saddle node bifurcation, limit cycles, Hopf bifurcation, etc.). 

## Requirements

Jupyter stats:

<img width="251" alt="jupyter stats" src="https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/37c92005-a1c7-4052-a8a3-6c9fb6d27200">


packages required for FHN-class:

     import networkx as nx
     import numpy as np
     import random as rd

packages required for visualizations:

     import numpy as np
     import matplotlib.pyplot as plt
     from scipy.signal import find_peaks
     import seaborn as sns
     from pathlib import Path
     import os
     from datetime import datetime
     from moviepy.editor import *



## Given network with local FitzHugh-Nagumo dynamics:
     coupling = 1
     G = nx.watts_strogatz_graph(100, 2, 0)
     a = 0.3
     b = 0.1
     tau = 5
     delta_t = 10**(-2)
     T = 5000
     
     FitzHugh_Nagumo_coupled(coupling=coupling, network=G, a=a, b=b, tau=tau, delta_t=delta_t, T=T, initial_states=None) 
          
CPU times: user 1min, sys: 11.5 s, total: 1min 12s
Wall time: 1min 16s

## Visualizations:

### Plot the node activity for both variables of the FitzHugh-Nagumo model
     G = nx.grid_2d_graph(3, 3) 
     model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-3), T=50) 
     adj_mat = nx.to_numpy_array(G) 
     act_mat=model.run(adj_mat) 
     plot_activity(act_mat)

![plot activity v for GitHub, grid 3x3, cpl=1, T=50, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/8cac99bf-cee1-4351-9e56-9bfc5d35f605)
![plot activity w for GitHub, grid 3x3, cpl=1, T=50, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/011a6f90-978b-4772-ab78-5ff3ef664ba4)


### Trajectories in the Phase Plane
     G = nx.watts_strogatz_graph(10, 2, 0) 
     a = 0.3
     b = 0.1
     tau = 5
     model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=a, b=b, tau=tau, delta_t=10**(-2), T=50) 
     adj_mat = nx.to_numpy_array(G) 
     act_mat=model.run(adj_mat) 
     plot_phase_portrait(a, b, tau, act_mat, force_field=True)

force_field=True draws arrows in the direction of the flow \
Note that:
- the nullclines are drawn in blue and orange
- the force_field is drawn for the differential equation without coupling! Therefore, it is not exact and meant to give intuition for the flow. 
         

![phase portrait for GitHub, cpl=1, N=10, k=2, T=50, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/7d703055-77f6-441c-b427-5a89e629ad9e)


 ## Pngs to GIF/Video
 
The following functions create sequences of png images which are used to create GIFs or videos with the functions GIF_creator() and video_maker() respectively. \
For videos there is an additional function stack_videos() to create one video out of four by stacking them in a 2x2 grid. \
There are several different ways to visualize the network dynamics:

### Evolution of the Dynamics on the Network 

The function image_creator visualized the node activity on the network. The network is plotted according to a chosen layout algorithm
and the node activity is color coded. \
Afterwards, one can make a GIF (used here) or video with the png images .

Here the initial states = "ins" are chosen such that the middle node in the grid network is perturbed and all other nodes are at the equilibrium. Observe the propagation of the perturbation away from the perturbed node throughout the network.

     G = nx.grid_2d_graph(11, 11) 
     model = FitzHugh_Nagumo_coupled(coupling=20, network=G, a=0.3, b=0.1, tau=0.1, delta_t=10**(-3), T=50, initial_states=ins) 
     act_mat = model.run(adj_mat=nx.to_numpy_array(G))
     pos = nx.spring_layout(G, iterations=800) 
     colormap = matplotlib.cm.twilight 
     stepstart = 0 
     stepsize = 200 

     image_creator(G, act_mat, pos, colormap, stepstart, stepsize)
     GIF_creator(0.1, '/Users/User/FitzHugh-Nagumo dynamics/pngs')

![blinking network gif for github, grid 11x11, a=0 3, b=0 1, tau=0 1, dt =0 001, T=50, cpl=20](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/35737e07-6dd4-49bd-8a5a-5788f9c92da7)


### Phase portrait:

The difference between runaround and plot_phase_portrait is that runaround illustrates every oscillator as a dot for a given time point.
The advantage is that for every time point it can be seen where the oscillators are in the phase plane.
     
     G = nx.watts_strogatz_graph(10, 2, 0)
     model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-2), T=100)
     act_mat = model.run(adj_mat)
     runaround(act_mat, 50, 10000, 50)
     video_maker(0.1, '/Users/User/FitzHugh-Nagumo dynamics/pngs', 'runaround')
     

https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/b65ef87e-f1fd-4804-96d5-6c73f63faeae






### Chimera plot

scatter_chim_gif is used to visualize chimera patterns in a network of identical, coupled oscillators. The function creates png images which can be used to make a GIF or video. 

     G = nx.watts_strogatz_graph(100, 20, 0)
     model = FitzHugh_Nagumo_coupled(coupling=100, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-2), T=500)
     act_mat = model.run(adj_mat)

     scatter_chim_gif(act_mat, 40000, 50)
     video_maker(0.1, '/Users/user/my/directory/pngs', 'chimera')

https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/11f1965b-9be0-4617-ab8c-6e1fbd9d84a8




## Coherence measure:       
The coherence measure $R(t) \in [0, 1]$ is a function that measures the synchronicity of the network of oscillators where $R(t) = 0$ corresponds to total incoherent and $R(t) = 1$ corresponds to total coherent. \
A modified version of the coherence measure $R(t)$ from the following paper is used: 
"The synchronization of FitzHugh–Nagumo neuron network coupled by gap junction" - 
Zhan Yong, Zhang Su-Hua, Zhao Tong-Jun, An Hai-Long, Zhang Zhen-Dong, Han Ying-Rong, Liu Hui, and Zhang Yu-Hong

### Coherence measure plot for several simulation runs
The plot_phase_coherence() function plots the coherence measure for both variable. The other visualizations only take the first/fast variable.

     number_simulation_runs = 3
     range_initial_states = 2
     coupling = 50
     network = nx.watts_strogatz_graph(250, 2, 0)
     a = 0.3
     b = 0.1
     tau = 5
     delta_t = 10**(-2)
     T = 1000

     plot_phase_coherence(number_simulation_runs, range_initial_states, coupling, network, a, b, tau, delta_t, T) =
     plot_phase_coherence(3, 2, 10, G, 0.3, 0.1, 5, 10**(-2), 1000)

![plot_phase_coherence,runs=3, init_states=2, ws(250,2,0), cpl=50, dt=0 01, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/bed0c7d3-c651-4d12-b9b2-7e8be5b7995f)


## Simulation runs and visualizations 
The coherence measure measures the synchronicity of the network of oscillators for all time steps. The function coh_gif() generates pngs images with a vertical line drawn at time steps of interest. The main use is to make a video or GIF to compare the dynamics from image_creator, scatter_chim_gif and runaround. One can stack these four visualization videos next to each other such that they run in parallel in one video. \
coh_gif() is mainly used in phase_coh_freq() function!

     G = nx.watts_strogatz_graph(50, 2, 0)
     model = FitzHugh_Nagumo_coupled(coupling=10, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-3), T=1500)
     
     phase_coherence_v = coherence values for a filtered simulation run
     simulation_run = a filtered run for which all visualizations are generated
     start = 0
     stepsize = 150
     
     coh_gif(phase_coherence_v, simulation_run, start, stepsize)
     video_maker(0.1, '/Users/user/my/directory/pngs', 'phase_coherence')



https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/ddfbb24e-787f-4d78-8e0e-a981caf155c6




### Run simulations and generate above visualizations:

The phase_coh_freq() - function combines the class and visualizations from above to make simulation runs and analyse them. \
Additionally, the activity matrix for the first variable v is plotted as heatmap to be able to link the different visualization together.\
Also, frequency analysis using the Fast Fourier Transform (FFT) is made for node activity and phase coherence measure.\
Note that: the main use of phase_coh_freq() is to make simulation runs and analyse individual runs. \
In comparison, the individual functions above can be used for single model realizations/simulation runs. \
Additionally, using stack_videos() one can stack the four out put videos to have the four visualiztions side by side as shown below.


    number_simulation_runs = 3
    range_initial_states = 2
    coupling = 50
    network = nx.watts_strogatz_graph(500, 12, 0)
    a = 0.3
    b = 0.1
    tau = 5
    delta_t = 10**(-2)
    T = 1000
    every_x_run = 10
    last_steps = 10000

    phase_coh_freq(number_simulation_runs, range_initial_states, coupling, network, a, b, tau, delta_t, T, every_x_run, last_steps) = 
    phase_coh_freq(3, 2, 10, G, 0.3, 0.1, 5, 10**(-2), 1000, 10, 10000)

![sim31 7run1, heatmap, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/2b1fd572-ddc5-4c63-982c-f0aabed9c96d)
![sim31 7run1, individual node analysis, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/113404c8-261e-4dec-8e7b-fb8e975081df)
![sim31 7run1, phase coh, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/04600d48-73c9-4f40-9ef2-39d3ae468f50)
![sim31 7run1, phase coh last 10000 steps, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/9685b655-a688-45b2-a082-c7429e1fd709)
![sim31 7run1, phase coh run1, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/6bac56ca-ee30-428b-b7c6-383772e34d09)
![sim31 7run1, freq ana phase coh run1, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/181aefdb-987e-423c-a562-1c1746200ebb)



https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/85d36c59-0a29-4880-9bb6-8d5da0b90994


https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/98622e82-c7ef-40a5-a2e4-bc66f90ebab6


https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/6fbc6ac0-4549-4c05-9923-d3977aa29870


https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/da2a8100-6a9b-4a2f-9b71-7a294e3361b0



### Stack videos
To make it easier to visually analyse the videos created by phase_coh_freq() one can put them in a 2x2 grid enabling to pause at times of interest. The videos have to be in the same directory as the Jupyter notebook running this code. \
Note that the videos have to have the same number of images and framerate.

    G = nx.watts_strogatz_graph(100, 12, 0)
    phase_coh_freq(3, 2, 20, G, 0.3, 0.1, 5, 10**(-2), 100, 10, 10000)

    video1 = "runaround.mp4"
    video2 = "blinking_network.mp4"
    video3 = "chimera.mp4"
    video4 = "phase_coherence.mp4"
    
    stack_videos(video1, video2, video3, video4)


https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/4185ae59-a120-46bb-a5a0-8fc117a9828a






