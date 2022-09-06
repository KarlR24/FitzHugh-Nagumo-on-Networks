# FitzHugh-Nagumo-on-Networks
Implementation of FitzHugh-Nagumo oscillators on a network.
Every node of the network is a FitzHugh-Nagumo oscillator which are coupled according to the nearest neighbor relations. \
Here the ODE is approximated according to the Euler method. \
The code is built according to fabridamicelli and rewritten for FitzHugh-Nagumo oscillators. \
https://github.com/fabridamicelli/kuramoto

FitzHugh-Nagumo for node i: \
$\dot{v_i} = v_i - \frac{v_i^3}{3} - w_i + k* \sum_{j =1,...,N} a_{ij} (v_i - v_j)$ \
$\dot{w_i} = \frac{1}{\tau} (v_i - bw_i + a)$ \
where $a_{ij}$ denotes the entries of the adjacency matrix (for a given network). Note that the sum is equivalent to summing only over neighboring nodes and omitting the adjacency matrix.

# How to use FitzHugh_Nagumo_coupled()
FitzHugh_Nagumo_coupled(coupling=1, network=None, a=None, b=None, tau=None, delta_t=10**(-3), T=10, initial_states=None) \
coupling: positive real value which gives the strength of the coupling of neighbors \
          Note: if we are at a very stable fixed point it can happen that the coupling is too weak to influence/perturbe its neighbors 
          
network: graph which gives the neighbor relations (ie. adjacency matrix) \
         If no network is specified a Watts Strogatz network with rewiring probability p = 0 is created.
         
a, b, tau: real numbers/ parameters of the FitzHugh-Nagumo model

dealta_t: small real value; step size for the Euler method

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


# Further use of class: 
With additional code: create pngs from act_mat to create GIF. Then, use GIF to visually inspect/observe behaviour of the dynamics on the network. 

Example: G = nx.grid_2d_graph(10, 10) \
model = FitzHugh_Nagumo_coupled(coupling=1, network=G, a=0.3, b=0.1, tau=0.1, delta_t=10**(-3), T=50, initial_states=ins) \
Here the initial_states are chosen such that one node in a corner is perturbed and all other nodes are at the equilibrium.


![10x10, one perturbed, twilight](https://user-images.githubusercontent.com/104760326/188586292-599d7e96-57f2-4e70-a445-8ad671d5f781.gif)

The parameters are chosen such that we have oscillatory behaviour and the colormap is only with respect to the first equation of FitzHugh-Nagumo. \
Observe the propagation of the pertubation awaz from perturbed node throughout the network.


