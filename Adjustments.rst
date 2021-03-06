========================================================
Temporary Modification of Variable in AgMIP Data Formats
========================================================
:Version: 1.0.1-DRAFT
:Date: 2015-08-14

.. contents::

--------
Overview
--------

Some models *may* support modifications which
allow variables to be modified over time with model control. This
is beneficial when simulating FACE experiments, shading studies or other research which temporarily modify environmental inputs. To provide
the models with this information from the AgMIP tools, the **Adjustments**
block is to be added to the root level of the ACE JSON Structure.

This is a DRAFT, please feel free to fork this on github and make
changes. Please submit a pull request for comment and merging into
the official document.

--------------
ACE Definition
--------------

The **adjustment** block has the ability to inform the translator
of the variable to be adjusted, how it is to be adjusted, by how much, at what schedule,
and, for layered data (soils), at what level this adjustment is to take place.

A simple example is TMAX increased by two degrees celsius starting on January 1, 2010 and ending June 15, 2010:

.. code-block:: javascript

    adjustments: [{"variable":"tmax",
                   "method":"delta",
                   "value":"2",
                   "startdate":"2010-01-01",
                   "enddate":"2010-06-15",
                  }]

Each adjustment object in the list **must** contain:

variable
    ICASA variable name

method
    Method to apply: delta, multiply, or substitute

    delta
        increases/decreases the original variable by the value 
    
    multiply
        multiplies the orignal variable by the value
   
    substitute
        replaces the original variable with the value

value
    The amount to apply to the variable by the method

Each adjustment **can** contain:

startdate
    A fixed or relative date to start the adjustment 
    If no startdate is specified, the changes will begin on the start of simulation date.
      CHP: need details on how a relative date would work. Offset from start of simulation or planting date? Do we need this?

enddate
    A fixed or relative date to remove the adjustment 
    If no enddate is specified, the changes will end at the end of simulation date.
      CHP: need details on how a relative date would work. Offset from start of simulation or planting date or startdate? Do we need this?

apply_to
    When dealing with soil layer data, which layer (by depth in cm) this modification is to apply to. 
    << weather data "layers" are covered with the date range, so do not need to be handled here. ARe there any other types of layer information or is this just for soils?
      CHP: by depth or by soil layer index? Soil layer index presents problems when there are layer modifications also applied in the DOME. I recommend using depth.


-------------
DOME Function
-------------

This example shows how the function would be called to adjust the SLOC variable in the
upper 5cm of a soil profile.

.. code-block::

    <new>, SLOC, ADJUST(), MULTIPLY, 0.95, , , <5

**NOTES**

1. The operator "<new>" represents a new operator that we need to decide upon. The current operators "REPLACE" and "FILL" will not work here because this is a temporary modification that will not overwrite the existing value of the variable, but replace it for a range of dates.
      CHP: I suggest that we use MODTEMP to replace <new> in the DOME function. 
2. Blank fields denote missing optional arguments for startdate and enddate. 
3. How the date and layer information should be handled is still being discussed.

*This format needs to be expanded upon*
