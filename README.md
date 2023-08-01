# FitzHugh-Nagumo dynamics on Networks
Python implementation of a network with local FitzHugh-Nagumo dynamics.
Every node of the network is a FitzHugh-Nagumo neuron which is coupled to its nearest neighbors. \
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

### Plot the node activity for both FitzHugh-Nagumo variables
     G = nx.grid_2d_graph(3, 3) 
     model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-3), T=50) 
     adj_mat = nx.to_numpy_array(G) 
     act_mat=model.run(adj_mat) 
     plot_activity(act_mat)
     
![plot activity v for GitHub, grid 3x3, T=50, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/1fd45b3d-a7ee-4398-a938-55342e3dc9fe)
![plot activity w for GitHub, grid 3x3, T=50, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/419bd9eb-2664-4a56-91d1-9c8d6b53bc98)



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
         

![phase portrait for GitHub, N=10, k=2, T=50, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/7d628eb0-70f7-49a0-815c-4b75ebd691a1)


 ## Pngs to GIF/Video
 
The following functions create sequences of png images which are used to create GIFs or videos with the functions GIF_creator() and video_maker() respectively. \
There are several different ways to visualize the network dynamics:

### Evolution of the Dynamics on the Network 

The function image_creator visualized the node activity on the network. The network is plotted according to a chosen layout algorithm
and the node activity is colorcoded. \
Afterwards, one can make a GIF (used here) or video with the png images .

Here the initial states = "ins" are chosen such that the middle node in the grid network is perturbed and all other nodes are at the equilibrium. Observe the propagation of the pertubation away from the perturbed node throughout the network.

     G = nx.grid_2d_graph(11, 11) 
     model = FitzHugh_Nagumo_coupled(coupling=20, network=G, a=0.3, b=0.1, tau=0.1, delta_t=10**(-3), T=50, initial_states=ins) 
     act_mat = model.run(adj_mat=nx.to_numpy_array(G))
     pos = nx.spring_layout(G, iterations=800) 
     colormap = matplotlib.cm.twilight 
     stepstart = 0 
     stepsize = 200 

     image_creator(G, act_mat, pos, colormap, stepstart, stepsize)
     GIF_creator(0.1, '/Users/User/FitzHugh-Nagumo dynamics/pngs', '/Users/User/FitzHugh-Nagumo dynamics/GIF')

![blinking network gif for github, grid 11x11, a=0 3, b=0 1, tau=0 1, dt =0 001, T=50, cpl=20](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/e145f83a-ed87-4926-8d69-633d12a6a577)


### Phase portrait:

The difference between runaround and plot_phase_portrait is that runaround illustrates every oscillator as a dot for a given time point.
The advantage is that for every time point it can be seen where the oscillators are in the phase plane.
     
     G = nx.watts_strogatz_graph(10, 2, 0)
     model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-2), T=100)
     act_mat = model.run(adj_mat)
     runaround(act_mat, 50, 10000, 50)
     video_maker(0.1, '/Users/User/FitzHugh-Nagumo dynamics/pngs', 'runaround')
     


https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/7f2121d6-35cc-43c1-b670-9ff5740227cc





### Chimera plot

scatter_chim_gif is used to visualize chimera patterns in a network of identical, coupled oscillators. The function creates png images which can be used to make a GIF or video. 

     G = nx.watts_strogatz_graph(100, 20, 0)
     model = FitzHugh_Nagumo_coupled(coupling=100, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-2), T=500)
     act_mat = model.run(adj_mat)

     scatter_chim_gif(act_mat, 40000, 50)
     video_maker(0.1, '/Users/user/my/directory/pngs', 'chimera')


https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/7216da03-6587-448c-ae3e-50f4d652cc77



## Coherence measure:       
The coherence measure $R(t) \in [0, 1]$ is a function that measures the synchronicity of the network of oscillators where $R(t) = 0$ corresponds to total incoherent and $R(t) = 1$ corresponds to total coherent. \
A modified version of the coherence measure $R(t)$ from the following paper is used: 
"The synchronization of FitzHugh–Nagumo neuron network coupled by gap junction" - 
Zhan Yong, Zhang Su-Hua, Zhao Tong-Jun, An Hai-Long, Zhang Zhen-Dong, Han Ying-Rong, Liu Hui, and Zhang Yu-Hong

### Coherence measure plot for several simulation runs
The plot_phase_cohere() function plots the coherence measure for both variable. The other visualizations only take the first/fast variable.

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
     
     coh_gif(phase_coherence_v, simulation_run, start, stepsize):
        



https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/644e1ed5-67a7-46f4-8226-7c2b12c428dd





### Run simulations and generate above visualizations:

The phase_coh_freq() - function combines the class and visualiations from above to make simulation runs and analyse them. \
Additionally, the activity matrix for the first variable v is plotted as heatmap to be able to link the different visualization together.\
Also, frequency analysis using the Fast Fourier Transform (FFT) is made for node activity and phase coherence measure.\
Note that: the main use of phase_coh_freq() is to make simulation runs and analyse individual runs. \
In comparison, the individual functions above can be used for sinlge model realizations/simulation runs.


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

![sim31 7run1, heatmap, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/6663a0f0-cf43-4746-8e4c-18ee612d140f)
![sim31 7run1, individual node analysis, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/1d0480df-481e-4bce-be97-2944a03bdee5)
![sim31 7run1, phase coh, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/7ab40318-a04a-4da6-8902-096f3534dff4)
![sim31 7run1, phase coh last 10000 steps, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/4bdc1275-2dba-4acb-8fbd-9c99970c675e)
![sim31 7run1, phase coh run1, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/0075dafe-05bf-4cec-9012-5f4289e59f96)
![sim31 7run1, freq ana phase coh run1, N=500, k=12, p=0, cpl=50, T=1000, dt=0 01](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/51f31d13-69be-480a-90e7-399152514223)



https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/8fb4c243-70c3-48db-996e-0de608caca4f

https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/53095b34-f9f9-4a7c-9dec-e1a0d8fa0a4e

https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/a8fe4499-fb0a-42ed-a219-4b7f830495d1

https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/0a64c4ab-a27a-4376-8fc7-19da4cce1d20






