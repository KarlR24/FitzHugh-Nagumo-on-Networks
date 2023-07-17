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

## Given network with local FitzHugh-Nagumo dynamics:
     FitzHugh_Nagumo_coupled(coupling=1, network=None, a=None, b=None, tau=None, delta_t=10**(-3), T=10, initial_states=None) 
          coupling: positive real value which gives the strength of the coupling of neighbors with default vaulue = 1
                    coupling = 0 corresponds to no coupling
                
          network: networkx graph 
                   network of interest
                   If no network is specified then a Watts-Strogatz network is created.
         
          a, b, tau: float 
                   parameters of the FitzHugh-Nagumo model 

          delta_t: float 
                   step size for the Euler method 

          T: integer 
             duration of the simulation

          initial_states: list or array containing two arrays of length number of nodes 
                    one with initial states for v and the other one for w 
                    If no initial states are given then random ones are created for every node. 
          

## Visualizations:

### Plot the node activity for both FitzHugh-Nagumo variables
     G = nx.grid_2d_graph(3, 3) 
     model = FitzHugh_Nagumo_coupled(coupling=2, network=G, a=0.3, b=0.1, tau=0.2, delta_t=10**(-3), T=20) 
     adj_mat = nx.to_numpy_array(G) 
     act_mat=model.run(adj_mat) 
     plot_activity(act_mat)


![Activity of first and second variable plotted against the time steps.](https://user-images.githubusercontent.com/104760326/188576765-893c7b97-43e5-458e-85de-4273d5efa95b.png)


### Trajectories in the Phase Plane
force_field=True draws arrows in the direction of the flow \
Note that the force_field is drawn for the differential equation without coupling! Therefore, it is not exact and meant to give intuition for the flow.

     G = nx.grid_2d_graph(3, 3) 
     model = FitzHugh_Nagumo_coupled(coupling=2, network=G, a=0.3, b=0.1, tau=0.2, delta_t=10**(-3), T=20) 
     adj_mat = nx.to_numpy_array(G) 
     act_mat=model.run(adj_mat) 
     plot_phase_portrait(0.3, 0.1, 0.2, act_mat, force_field=True)
         

![download](https://user-images.githubusercontent.com/104760326/188612773-91fcc9eb-a876-48ba-b8ce-1a9c740a492f.png)


 ## Pngs to GIF/Video
 
The following functions create sequences of png images which are used to create GIFs/videos. \
There are several different ways to visualize the network dynamics:

### Evolution of the Dynamics on the Network 

The function image_creator visualized the node activity on the network. The network is plotted according to a chosen layout algorithm
and the node activity is colorcoded. \
Afterwards, one can make a GIF (used here) or video with the png images .

Here the initial_states are chosen such that one node in a corner is perturbed and all other nodes are at the equilibrium. Observe the propagation of the pertubation away from perturbed node throughout the network.

     G = nx.grid_2d_graph(10, 10) 
     model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=0.1, delta_t=10**(-3), T=50, initial_states=ins) 
     act_mat = model.run(adj_mat=nx.to_numpy_array(G))
     pos = nx.spring_layout(G, iterations=500) 
     colormap = matplotlib.cm.twilight 
     stepstart = 100 
     stepsize = 250 

     image_creator(G, act_mat, pos, colormap, stepstart, stepsize)
     GIF_creator(0.05, '/Users/User/FitzHugh-Nagumo dynamics/pngs', '/Users/User/FitzHugh-Nagumo dynamics/GIF')


![10x10, one perturbed, twilight](https://user-images.githubusercontent.com/104760326/188586292-599d7e96-57f2-4e70-a445-8ad671d5f781.gif)


### Phase portrait:

The difference between runaround and plot_phase_portrait is that runaround illustrates every oscillator as a dot for a given time point.
The advantage is that for every time point it can be seen where the oscillators are in the phase plane.
     
     G = nx.watts_strogatz_graph(10, 2, 0)
     model = FitzHugh_Nagumo_coupled(coupling=0.5, network=G, a=1, b=0.1, tau=5, delta_t=10**(-3), T=500)
     act_mat = model.run(adj_mat)
     runaround(act_mat, 50, 500)
     GIF_creator(0.05, '/Users/User/FitzHugh-Nagumo dynamics/pngs', '/Users/User/FitzHugh-Nagumo dynamics/GIF')
     
![10 node ring, cpl=0.5, a=1, b=0.1, tau=5, T=500, runaround](https://user-images.githubusercontent.com/104760326/200849817-55175359-92ac-491a-84db-b8a2a526a184.gif)


### scatter_chim_gif(act_mat, start, stepsize)

To visualize chimera states in the network of coupled oscillators scatter_chim_gif creates png images to make a GIF/video.

     scatter_chim_gif(act_mat, start, stepsize):  
          '''
          act_mat: nd array
          activity matrix from model
        
          stepstart: integer 
               starting time to begin taking pictures to make a GIF
               stepstart < T/delta_t, ie. total number of steps in model
               note: this GIF is for "later times" where the network has reached (some kind of) synchronicity
        
          stepsize: integer
          every stepsize-number of timestep is taken from the activity matrix
          '''
Example: G = nx.watts_strogatz_graph(100, 14, 0)\
model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=5, delta_t=10**(-3), T=100)\
act_mat = model.run(adj_mat)\

scatter_chim_gif(act_mat, 95.000, 50)
video_maker(0.1, '/Users/user/my/directory/pngs')



https://user-images.githubusercontent.com/104760326/228857148-89eddbb7-cc18-498d-b9db-7050479340e3.mp4






## Phase Coherence measure:

     
     plot_phase_coherence(number_simulation_runs, range_initial_states, coupling, network, a, b, tau, delta_t, T):
          number_simulation_runs: integer
               number of simulations to run for phase coherence plots
    
          range_initial_states: positiv integer
               size of rectangle around origin from which initial states are drawn according to uniform distribution
    
          coupling: float
               coupling strength between oscillators
    
          network: networkx graph
        
          a, b, tau: float
               parameters for FitzHugh-Nagumo
    
          delta_t, T: float
               for Euler approximation of the differential equations
          
Note that new random initial states are generated for every simulation run. Every simulation run is one curve in the plot which describes the phase coherence (synchronicity of the oscillators) in the network.
Note that, the acitivity matrix is not stored therefore not available for further analysis. The output of this function is a plot of the phase coherence measure for both variables v, w.

phase coherence measure $R(t)$ from the paper: 
"The synchronization of FitzHugh–Nagumo neuron network coupled by gap junction" - 
Zhan Yong, Zhang Su-Hua, Zhao Tong-Jun, An Hai-Long, Zhang Zhen-Dong, Han Ying-Rong, Liu Hui, and Zhang Yu-Hong
    
$R(t) =  \frac{1}{N^2} * \sum_{i,j} \langle [v_i(t) - v_j(t)]^2 \rangle $
        
                    
where $\langle \rangle$ denotes the average of a stochastic random variable.
    
Example: For a ring graph with 25 nodes we want to run the dynamics with non-oscillatory parameter choice on it 100 times where for every simulation run new initial states are generated. The initial states are picked according to a uniform distribution and should be in the interval $[-1, 1]$. The rest of the parameters are in the same order as they are in the FitzHughNagumo_on_network class:\
number_simulation_runs = 100 \
initial_states_interval = 1\
coupling = 1\
network = nx.erdos_renyi_graph(25, 0.35)\
a = 1.3\
b = 0.7\
tau = 200/3\ 
delta_t = 10**(-3)\
T = 600\

plot_phase_coherence(number_simulation_runs, initial_states_interval, coupling, network, a, b, tau, delta_t, T) =

plot_phase_coherence(100, 1, 1, G, 1.3, 0.7, 200/3, 10**(-3), 600)

![Screenshot 2023-05-24 at 17 01 23](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/99b0add9-7ffc-47a7-8a5d-ef0f5ef93769)


### coh_gif(phase_coherence_v, simulation_run, stepsize)

To compare the dynamics from image_creator, scatter_chim_gif and runaround we make a GIF/video where a vertical bar is at the current time step. This function is used in the code of phase_coh_freq below!

     coh_gif(phase_coherence_v, simulation_run, stepsize):
          '''
          phase_coherence_v: 2d-array
               phase coherence values for every time step
        
          simulation_run: integer
               simulation run of interest to make GIF
    
          stepsize: float
               after stepsize many iterations the next png is created
          '''
    
Example: Since coh_gif takes its imputs from variables inside the phase_coh_freq function. Therefore, this example only showcases the output images compiled into a video.



https://user-images.githubusercontent.com/104760326/228861323-ac74ffc4-0a10-4013-8a87-663674978003.mp4



### phase_coh_freq():
phase_coh_freq(number_simulation_runs, range_initial_states, coupling, network, a, b, tau, delta_t, T, every_x_run, last_steps):

     number_simulation_runs: integer
        number of simulations to run for phase coherence plots
    
    range_initial_states: positiv integer
        size of rectangle around origin from which initial states are drawn according to uniform distribution
    
    coupling: float
        coupling strength between oscillators
    
    network: networkx graph
        
    a, b, tau: float
        parameters for FitzHugh-Nagumo
    
    delta_t, T: float
        for Euler approximation of the differential equations
        
    every_x_run: integer
        for every x-th run in the simulation, the individual node dynamics will be analysis and GIFs/videos & plots generated
        
    last_steps: interger (< T/delta_t)
        number of time units/steps where eg. phase coherence frequency is analysed and chimera GIF are generated
        want to analyse time where network has reached a stable state of (some degree of) synchronicity
        
This function combines the class and visualiation function from above to make simulation runs and analyse them in one go. \
Additionally, the activity matrix for the first variable v is plotted as heatmap to be able to link the different visualization together.\
Furthermore, the node activity and the phase coherence measure are decomposed into their frequencies using Fast Fourier Transform (FFT).\
Note that: phase_coh_freq purpose is to make simulation runs and analysis individual runs with the same function. \
In comparison, the individual functions above can be used for sinlge model realizations to test and visualize

Example
number_simulation_runs = 3
range_initial_states = 3
coupling = 1
network = nx.watts_strogatz_graph(500, 20, 0)
a = 0.3
b = 0.1
tau = 5
delta_t = 10**(-2)
T = 1000
every_x_run = 10
last_steps = 10000

phase_coh_freq(number_simulation_runs, range_initial_states, coupling, network, a, b, tau, delta_t, T, every_x_run, last_steps) = 

phase_coh_freq(3, 3, 1, G, 0.3, 0.1, 5, 10**(-2), 1000, 10, 10000)


![sim1 5run1, heatmap, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/f3b6fb85-6da8-4cca-9d25-cef403f15405)
![sim1 5run1, node activity, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/14ba652c-34b0-4a03-ac4e-682534c4997a)
![sim1 5run1, freq ana random nodes, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/8df5a115-6b18-4b2a-86ff-b2eeb7ff7d93)
![sim1 5run1, node activity node 1, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/3996895b-c773-4ff1-96ca-36fc8263f54a)
![sim1 5run1, feq ana node 1, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/a96d4084-192d-4afc-a848-e32bb6cc296a)
![sim1 5run1, phase coherence, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/1c2b9ee5-02a0-4a45-815b-660f14119671)
![sim1 5run1, phase coherence last 10000 steps, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/83795edb-d77c-49cc-8339-949c5de39f63)
![sim1 5run1, freq ana phase coherence, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/b38e574b-88fd-463c-924e-776e8c748239)
![sim1 5run1, phase coherence filtered 1, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/3538d6af-a5e2-42fd-b66f-d7162e70e3ae)
![sim1 5run1, freq ana phase coherence filtered 1, 500 nodes, k=20, T=1000](https://github.com/KarlR24/FitzHugh-Nagumo-on-Networks/assets/104760326/ca12d384-e350-41d3-a0c2-1157651de9d8)









