# FitzHugh-Nagumo-on-Networks
Implementation of FitzHugh-Nagumo oscillators on a network.
Every node of the network is a FitzHugh-Nagumo oscillator which is coupled to its nearest neighbors. \
Here, the ODE is approximated according to the Euler method. \
The code is built according to fabridamicelli and rewritten for FitzHugh-Nagumo oscillators. \
https://github.com/fabridamicelli/kuramoto

FitzHugh-Nagumo for node i: \
$\dot{v_i} = v_i - \frac{v_i^3}{3} - w_i + \frac{k}{I_i}* \sum_{j =1,...,N} a_{ij} (v_i - v_j)$ \
$\dot{w_i} = \frac{1}{\tau} (v_i - bw_i + a)$ \
where $a_{ij}$ denotes the entries of the adjacency matrix (for a given network) and $I_i$ is the number of incoming interactions for node i (ie. degree of node i). Note that the sum is equivalent to summing only over neighboring nodes and omitting the adjacency matrix. \
Remark: Depending on the choice of parameters in the equations the behaviour of the dynamics changes drastically. E.g. the choice of parameters in the ReadMe is such that the system exhibits oscillatory behaviour.

# Class FitzHugh_Nagumo_coupled()
     FitzHugh_Nagumo_coupled(coupling=1, network=None, a=None, b=None, tau=None, delta_t=10**(-3), T=10, initial_states=None) 
          coupling: positive real value which gives the strength of the coupling of neighbors with 
                    default vaulue = 1
                    Note: if we are at a very stable fixed point it can happen that the coupling is 
                    too weak to influence/perturbe its neighbors 
                    usually the coupling strenght is between 0 (= no coupling & only local dynamics) and 2.
                
          network: graph on which dynamics run. Also gives the neighbor relations (ie. adjacency matrix) 
                   If no network is specified a Watts Strogatz network with rewiring probability p = 0 is created.
         
          a, b, tau: float/ parameters of the FitzHugh-Nagumo model 
                   Note: depending on the choice of parameters the dynamics exhibit different qualitative 
                         behaviours (saddle node bifurcation, limit cycles, Hopf bifurcation, etc.)

          delta_t: small real value; step size for the Euler method 

          T: integer; duration of the simulation

          initial_states: list or array containing two arrays of length number of nodes: one with initial 
                    states for v and the other one for w 
                    If no initial states are given then random ones are created for every node. 
                    Sidenote: either network or initial_states must be given
          

# Visualizations:

# plot_activity(act_mat)
This plots the node activity for all nodes for all times.

Example: \
G = nx.grid_2d_graph(3, 3) \
model = FitzHugh_Nagumo_coupled(coupling=2, network=G, a=0.3, b=0.1, tau=0.2, delta_t=10**(-3), T=20) \
adj_mat = nx.to_numpy_array(G) \
act_mat=model.run(adj_mat) \
plot_activity(act_mat)


![image](https://user-images.githubusercontent.com/104760326/188576765-893c7b97-43e5-458e-85de-4273d5efa95b.png)


# plot_phase_portrait(a, b, tau, act_mat, force_field=optional)
force_field=True draws arrows in the direction of the flow \
Note that the force_field is drawn for the differential equation without coupling! Therefore, it is not precise but accurate enough to get an idea of the flow.

Example: same example as for plot activity \
          G = nx.grid_2d_graph(3, 3) \
          model = FitzHugh_Nagumo_coupled(coupling
          =2, network=G, a=0.3, b=0.1, tau=0.2, delta_t=10**(-3), T=20) \
          adj_mat = nx.to_numpy_array(G) \
          act_mat=model.run(adj_mat) \
          plot_phase_portrait(0.3, 0.1, 0.2, act_mat, force_field=True)
         

![download](https://user-images.githubusercontent.com/104760326/188612773-91fcc9eb-a876-48ba-b8ce-1a9c740a492f.png)


 # Pngs to GIF/Video
 
The following functions create sequences of png images and use them to create GIFs/videos. \
There are several different ways to visualize the network dynamics:

# image_creator(network, act_mat, pos, colormap, stepstart, stepsize)

     The function image_creator visualized the node activity on the network. The network is plotted according to a specified layout 
     and the node activity is colorcoded. \
     Afterwards, one can make a GIF (used here) or video out of the png images 

     image_creator(G, act_mat, pos, colormap, stepstart, stepsize):
          G: networkX graph
    
          act_mat: ndarray
    
          pos: dictionary with nodes as keys and positions as values
               layouts like nx.circular_layout, nx.spring_layout, etc.
    
          colormap: matplotlib.cm.colormap
               colormap for node states
        
          stepstart: float
               iteration step where first png is created
    
          stepsize: float
               after stepsize many iterations the next png is created
    
    
   
     GIF_creator(duration, png_dir, path):
          duration: float
               time each picture is shown in GIF
               useful range is between 0.05 to 0.2 
        
          png_dir: string
               direction where .pngs are to make GIF out of
    
          path: string
               direction where the GIF should be saved
        
    this code is from stackoverflow.com
    
    
    
    
Example: Here the initial_states are chosen such that one node in a corner is perturbed and all other nodes are at the equilibrium. \
G = nx.grid_2d_graph(10, 10) \
model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=0.1, delta_t=10**(-3), T=50, initial_states=ins) \
act_mat = model.run(adj_mat=nx.to_numpy_array(G))\
pos = nx.spring_layout(G, iterations=500) \
colormap = matplotlib.cm.twilight \
stepstart = 100 \
stepsize = 250 

The image_creator saves the created pngs in the directory into a folder named "pngs". This folder has to be created beforehand manually. 


image_creator(G, act_mat, pos, colormap, stepstart, stepsize)

GIF_creator(0.05, '/Users/User/FitzHugh-Nagumo dynamics/pngs', '/Users/User/FitzHugh-Nagumo dynamics/GIF')


The parameters are chosen such that we have oscillatory behaviour and the colormap is only with respect to the first equation of FitzHugh-Nagumo. \
Observe the propagation of the pertubation away from perturbed node throughout the network.


![10x10, one perturbed, twilight](https://user-images.githubusercontent.com/104760326/188586292-599d7e96-57f2-4e70-a445-8ad671d5f781.gif)


# run around:

     runaround visualizes the node activity in the phase plane where the first variable v is on the x-axis and the second variable w is on the y-axis.
     
     runaround(act_mat, stepstart, stepend, stepsize)
          act_mat: array
               states of oscillators for all times
          stepstart: int
               time step where first png is created
          stepend: int
               time step where last png is created
          stepsize: int
               after stepsize many timesteps/iterations the next png is created
               
Like the image_creator above run_around creates and stores png images in a folder "pngs".

Example: G = nx.watts_strogatz_graph(10, 2, 0)\
model = FitzHugh_Nagumo_coupled(coupling=0.5, network=G, a=1, b=0.1, tau=5, delta_t=10**(-3), T=500)\
act_mat = model.run(adj_mat)\
runaround(act_mat, 50, 500)\
Now compile pngs into GIF with GIF_creator.

![10 node ring, cpl=0.5, a=1, b=0.1, tau=5, T=500, runaround](https://user-images.githubusercontent.com/104760326/200849817-55175359-92ac-491a-84db-b8a2a526a184.gif)


# scatter_chim_gif(act_mat, start, stepsize)

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






# Phase Coherence measure:

     
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
Note that, the acitivity matrix is not stored therefore not for further analysis available. The output of this functions a plot phase coherence measure for both variables v, w.

phase coherence measure $R(t)$ from the paper: 
"The synchronization of FitzHugh–Nagumo neuron network coupled by gap junction" - 
Zhan Yong, Zhang Su-Hua, Zhao Tong-Jun, An Hai-Long, Zhang Zhen-Dong, Han Ying-Rong, Liu Hui, and Zhang Yu-Hong
    
$R(t) =  \frac{1}{N^2} * \sum_{i,j} \langle [v_i(t) - v_j(t)]^2 \rangle $
        
                    
where $\langle \rangle$ denotes the average of a stochastic random variable.
    
Example: For a ring graph with ten nodes we want to run the dynamics with non-oscillatory parameter choice on it ten times where for every simulation run new initial states are generated. The initial states are picked according to a uniform distribution and should be in the interval $[-5, 5]$. The rest of the parameters are in the same order as they are in the FitzHughNagumo_on_network class:\
number_simulation_runs = 10 \
initial_states_interval = 5\
coupling = 1\
network = nx.watts_strogatz_graph(10, 2, 0)\
a = 1\
b = 1\
tau = 1\ 
delta_t = 10**(-3)\
T = 100\

plot_phase_coherence(number_simulation_runs, initial_states_interval, coupling, network, a, b, tau, delta_t, T) =

plot_phase_coherence(10, 5, 1, G, 1, 1, 1, 10**(-3), 100)

![phase coh v, non oscil](https://user-images.githubusercontent.com/104760326/199758056-a11e7743-0ba5-4cc7-b47f-c89787318c5d.png)

![phase coh w, non oscil](https://user-images.githubusercontent.com/104760326/199758091-be5a3d21-11ff-4dbd-a987-9096daec744d.png)


# coh_gif(phase_coherence_v, simulation_run, stepsize)

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



# phase_coh_freq():
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
        for every x-th run in the simulation, the individual node dynamics will be analysis and GIFs & plots generated
        
    last_steps: interger (< T/delta_t)
        number of time units/steps where eg. phase coherence frequency is analysed and chimera GIF are generated
        want to analyse time where network has reached a stable state of (some degree of) synchronicity
        
This function combines the functions above to make simulation runs and analysing them in one go. 
Note that: the functions above are written for single model realizations whereas this function is meant for several simulation runs and the analysis
of individual runs (eg. frequency analysis of the phase coherence measure and node activity and chimera, runaround and blinking network GIF).
