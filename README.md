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

# How to use FitzHugh_Nagumo_coupled()
FitzHugh_Nagumo_coupled(coupling=1, network=None, a=None, b=None, tau=None, delta_t=10**(-3), T=10, initial_states=None) \
          coupling: positive real value which gives the strength of the coupling of neighbors with default vaulue = 1\
                   Note: if we are at a very stable fixed point it can happen that the coupling is too weak to influence/perturbe its neighbors \
                   usually the coupling strenght is between 0 (= no coupling & only local dynamics) and 2.
                
          network: graph on which dynamics run. Also gives the neighbor relations (ie. adjacency matrix) \
                   If no network is specified a Watts Strogatz network with rewiring probability p = 0 is created.
         
          a, b, tau: float/ parameters of the FitzHugh-Nagumo model \
                   Note: depending on the choice of parameters the dynamics exhibit different qualitative behaviours (saddle node bifurcation, limit                                cycles, Hopf bifurcation, etc.)

          delta_t: small real value; step size for the Euler method 

          T: integer; duration of the simulation

          initial_states: list or array containing two arrays of length number of nodes: one with initial states for v and the other one for w \
          If no initial states are given then random ones are created for every node. \
          Sidenote: either network or initial_states must be given
          
# Use plot_activity(act_mat)
Example: \
G = nx.grid_2d_graph(3, 3) \
model = FitzHugh_Nagumo_coupled(coupling=2, network=G, a=0.3, b=0.1, tau=0.2, delta_t=10**(-3), T=20) \
adj_mat = nx.to_numpy_array(G) \
act_mat=model.run(adj_mat) \
plot_activity(act_mat)


![image](https://user-images.githubusercontent.com/104760326/188576765-893c7b97-43e5-458e-85de-4273d5efa95b.png)


# Use plot_phase_portrait
force_field=True draws arrows in the direction of the flow 

Example: same example as for plot activity \
          G = nx.grid_2d_graph(3, 3) \
          model = FitzHugh_Nagumo_coupled(coupling
          =2, network=G, a=0.3, b=0.1, tau=0.2, delta_t=10**(-3), T=20) \
          adj_mat = nx.to_numpy_array(G) \
          act_mat=model.run(adj_mat) \
          plot_phase_portrait(0.3, 0.1, 0.2, act_mat, force_field=True)
         

![download](https://user-images.githubusercontent.com/104760326/188612773-91fcc9eb-a876-48ba-b8ce-1a9c740a492f.png)


# Further use of class: 
With Make_images_and_Gif: create pngs from act_mat to create GIF. Then, use GIF to visually inspect/observe behaviour of the dynamics on the network. 

image_creator(G, act_mat, pos, colormap, stepstart, stepsize):

    '''
    G: network
    
    act_mat: array
    
    pos: dictionary with nodes as keys and positions as values
    
    colormap: matplotlib.cm.colormap
        colormap for node states
        
    stepstart: float
        iteration step where first png is created
    
    stepsize: float
        after stepsize many iterations the next png is created
    '''
    
   
GIF_creator(duration, png_dir, path):

    '''
    duration: float
        time each picture is shown in GIF
        useful range is between 0.05 to 0.2 
        
    png_dir: string
        direction where .pngs are to make GIF out of
    
    path: string
        direction where the GIF should be saved
        
    this code is from stackoverflow.com
    '''
    
    
    
Example: Here the initial_states are chosen such that one node in a corner is perturbed and all other nodes are at the equilibrium. \
G = nx.grid_2d_graph(10, 10) \
model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=0.1, delta_t=10**(-3), T=50, initial_states=ins) \
act_mat = model.run(adj_mat=nx.to_numpy_array(G))
pos = nx.spring_layout(G, iterations=500) \
colormap = matplotlib.cm.twilight \
stepstart = 100 \
stepsize = 250 \
The image_creator saves the created pngs in the directory into a folder named "pngs". This folder has to be created beforehand manually. \


image_creator(G, act_mat, pos, colormap, stepstart, stepsize)

GIF_creator(0.05, '/Users/User/FitzHugh-Nagumo dynamics/pngs', '/Users/User/FitzHugh-Nagumo dynamics/GIF')


The parameters are chosen such that we have oscillatory behaviour and the colormap is only with respect to the first equation of FitzHugh-Nagumo. \
Observe the propagation of the pertubation away from perturbed node throughout the network.


![10x10, one perturbed, twilight](https://user-images.githubusercontent.com/104760326/188586292-599d7e96-57f2-4e70-a445-8ad671d5f781.gif)





