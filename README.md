# FitzHugh-Nagumo-on-Networks
Implementation of FitzHugh-Nagumo oscillators on a network.
Every node of the network is a FitzHugh-Nagumo oscillator which are coupled according to the nearest neighbor relations. \
Here the ODE is approximated according to the Euler method.

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
either network or initial_states must be given
          
