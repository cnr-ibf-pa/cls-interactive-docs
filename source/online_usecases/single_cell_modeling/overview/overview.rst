.. _scm_overview:

########
Overview
########

The Single Cell Building Use Cases show practical examples of how to obtain a 
single cell model of morphologically and biophysically detailed neurons 
exploiting the Blue Brain Python Optimization Library (BluePyOpt, Van Geit 
W *et al.*, 2016, `Front. Neuroinform <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4896051/#>`_. 10:17. doi: `10.3389/fninf.2016.00017 <http://journal.frontiersin.org/article/10.3389/fninf.2016.00017/full>`_).

All Use Cases in this section use the current, public, version of the 
BluePyOpt. However, the different scientists implementing an optimization for 
a specific cell have used their own organization for the configuration files.

.. _hippocampal-neurons:

===================
Hippocampal neurons
===================

For the Use Cases referring to hippocampal cells, the different Use Cases will 
guide you through the most appropriate workflow, depending on your goal.
In general, they are organized around two different possibilities of 
increasing complexity:

1) Rebuild an existing single cell model
2) Build your own cell model using HBP data

They differ in the amount and type of information that is required to 
configure and run an optimization. Below is a short description of all files 
required for an optimization run for hippocampal cells. In the simplest case, 
all required files will be automatically copied into your Collab, and you just 
have to select items, push buttons, and wait to collect the results. 
You may want to change something (initial parameters, channel distribution, 
electrophysiological features, etc), but this requires more detailed knowledge.

To run the optimization, you need to have a morphology, a set of mod files for 
the ion channel or special mechanism kinetics, the stimulation protocols to be 
used (typically step current clamp stimuli), and the electrophysiological 
features you would like to use as a reference for the optimization.

The above information is contained in four self-consistent JSON files. Self-consistency is ensured by starting all json files used for a specific optimization with the same string, as shown below.

**********
morph.json
**********
Defines the Neurolucida ASCII file containing the 3D reconstruction to be used for the optimization.
A typical file looks like this:

.. code-block:: json

    {
	"INT_cAC_noljp": "011017HP2.asc"
    }

Where the first string is the optimization name, arbitrarily chosen by the user, and the second one is the name of the morphology file.

*************
features.json
*************
Contains the electrophysiological characteristics (spike event features, spike shape features, voltage features etc.) that will be used as a reference by the optimization process. In general, this file can be automatically generated by the eFEL GUI app. The different sections list the various features of interest.
A typical section looks like this:

.. code-block:: json

    {
        "INT_cAC_noljp": {
       	    "step_-0.4": {
                "soma": {
                    "voltage_deflection": [-19.772462606879138,  2.0331488835160232],
                    "voltage_base": [-74.617595464571281,  2.1406043489404434 ]
                }
            }
    }

Again, the first string is the name of the optimization, and in this case the user was interested in optimizing the *voltage_deflection* and *voltage_base* measured at the soma under a somatic current injection of -0.4 nA.

**************
protocols.json
**************

Defines the stimulation protocols that should be used for the optimization. This file is also automatically created by the eFEL GUI app, and it is matched with the information contained in the **features.json** file.
A typical section looks like this:

.. code-block:: json

    {
        "INT_cAC_noljp": {
       	    "step_-0.4": {
                "stimuli": [
                    {
                    	"delay": 531.0,
                    	"amp": -0.4,
                    	"duration": 400.0,
                    	"totduration": 1131.0
                    },
                    {
                    	"delay": 0.0,
                    	"amp": 0.0,
                    	"duration": 1131.0,
                    	"totduration": 1131.0
                    }
                ]
            }
    }

***************
parameters.json
***************

This is where a user can strongly determine the quality of the optimization. 
In this file, active and passive properties (and their distribution) are 
defined.

There are four self-explanatory main blocks defining: 1) the channels to be 
used in the different regions of a neuron (???mechanisms???), 2) their non-uniform 
distribution (???distributions???), 3) which parameters should be fixed (???fixed???) 
and 4) which parameters must be optimized (???optimized???).
Mechanism and parameter names depend on the set of mod files the user 
chooses to equip the cell with.
A typical section looks like this:

.. code-block:: json

    {
        "INT_cAC_noljp": {
       	    "mechanisms": {
        	"all":      ["pas","kdrb", "nax", "kap"],
        	"somatic":  ["kdb","kmb"],
        	"axonal":   ["kmb"],
        	"allnoaxon":["hd", "can", "cal", "cat", "kca", "cacumb","cagk"]
    	    },
    	    "distributions": {
    	    },
    	    "fixed": {
        	"global":    [["v_init", -80], ["celsius", 34]],
        	"all":       [["cm", 1, "secvar"],["ena", 50, "secvar"],["ek", -90, "secvar"]]
    	    },
    	    "optimized": {
                 "axonal": [
            	    ["gbar_nax", 0.025, 0.15, "uniform" ],
            	    ["gkdrbar_kdrb", 0.01, 0.08, "uniform" ],
                    ["gkabar_kap", 0.001, 0.04, "uniform" ],
                    ["gbar_kmb", 0.004, 0.05, "uniform" ],
                    ["Ra", 50, 300, "secvar"],
                    ["g_pas", 1e-6, 8e-5, "uniform" ],
                    ["e_pas", -95, -75, "uniform" ]
               ],
            }
        }
    }

The BluePyOpt must be configured to set the search algorithm. This is done by 
defining the offspring size and the max number of generations.
Finally, these Use Cases require computing resources on a HPC system. In order 
to carry out the optimization, a user must have an account on the Neuroscience 
Gateway or on one of the HPC systems supporting the Brain Simulation Platform 
activity, such as CINECA or JSC.

For details on NSG see |nsg_link|

.. |nsg_link| raw:: html  

    <a href="https://www.nsgportal.org/" target="_blank">Neuroscience Gateway</a>.
