CALIBRATOR (0.4.5 version)
===========================

A software to perform calibrations or optimizations of empirical parameters.

AUTHORS
-------

* Javier Burguete Tolosa (jburguete@eead.csic.es)
* Borja Latorre Garcés (borja.latorre@csic.es)

REQUIRED
--------

* mpicc, gcc or clang (to compile the source code)
* make (to build the executable file)
* autoconf (to generate the Makefile in different operative systems)
* automake (to check the operative system)
* pkg-config (to find the libraries to compile)
* gsl (to generate random numbers)
* libxml (to deal with XML files)
* gthreads (to use multicores in shared memory machines)
* glib (extended utilities of C to work with data, lists, mapped files, regular
	expressions, ...)
* [genetic](https://github.com/jburguete/genetic) (genetic algorithm)
* openmpi or mpich (optional: to run in parallelized tasks)
* doxygen (optional: standard comments format to generate documentation)
* latex (optional: to build the PDF manuals)

FILES
-----

* configure.ac: configure generator.
* Makefile.in: Makefile generator.
* config.h.in: config header generator.
* calibrator.c: source code.
* Doxyfile: configuration file to generate doxygen documentation.
* TODO: tasks to do.
* README.md: this file.
* tests/testX/*: several tests to check the program working.

BUILDING INSTRUCTIONS
---------------------

This software has been built and tested in the following operative systems:

Debian Linux 7.7
________________
Debian kFreeBSD 7.7
___________________
DragonFly BSD 4.0.1
___________________
FreeBSD 10.0
____________
NetBSD 6.1.5
____________

1. Download the latest [genetic](https://github.com/jburguete/genetic)

2. Dowload this repository

3. Link the latest genetic version to genetic doing on a terminal:
> $ cd PATH_TO_CALIBRATOR/0.4.5
>
> $ ln -s PATH_TO_THE_GENETIC/0.6.0 genetic

4. Build doing on a terminal:
> $ cd PATH_TO_CALIBRATOR/0.4.5
>
> $ aclocal
>
> $ autoconf
>
> $ automake --add-missing
>
> $ ./configure
>
> $ make

OpenBSD 5.6
___________

1. Download the latest [genetic](https://github.com/jburguete/genetic)

2. Dowload this repository

3. Link the latest genetic version to genetic doing on a terminal:
> $ cd PATH_TO_CALIBRATOR/0.4.5
>
> $ ln -s PATH_TO_THE_GENETIC/0.6.0 genetic

4. Build doing on a terminal:
> $ export AUTOCONF_VERSION=2.69 AUTOMAKE_VERSION=1.14
>
> $ cd PATH_TO_CALIBRATOR/0.4.5
>
> $ aclocal
>
> $ autoconf
>
> $ automake --add-missing
>
> $ ./configure
>
> $ make

MAKING REFERENCE MANUAL INSTRUCTIONS (file latex/refman.pdf)
------------------------------------------------------------

> $ cd PATH_TO_CALIBRATOR/0.4.5
>
> $ doxygen
>
> $ cd latex
>
> $ make

USER INSTRUCTIONS
-----------------

* Command line in sequential mode:
> $ ./calibrator [-nthreads X] input_file.xml

* Command line in parallelized mode (where X is the number of threads to open in
every node):
> $ mpirun [MPI options] ./calibrator [-nthreads X] input_file.xml

* The sintaxis of the simulator has to be:
> $ ./simulator_name input_file_1 [input_file_2] [input_file_3] [input_file_4] output_file

* The sintaxis of the program to evaluate the objetive function has to be (where
the first data in the results file has to be the objective function value):
> $ ./evaluator_name simulated_file data_file results_file

INPUT FILE FORMAT
-----------------

    <?xml version="1.0"/>
    <calibrate simulator="simulator_name" evaluator="evaluator_name" algorithm="algorithm_type" simulations="simulations_number" iterations="iterations_number" tolerance="tolerance_value" bests="bests_number" population="population_number" generations="generations_number" mutation="mutation_ratio" reproduction="reproduction_ratio" adaptation="adaptation_ratio">
        <experiment name="data_file_1" template1="template_1_1" template2="template_1_2" .../>
        ...
        <experiment name="data_file_N" template1="template_N_1" template2="template_N_2" .../>
        <variable name="variable_1" minimum="min_value" maximum="max_value" format="c_string_format" sweeps="sweeps_number" bits="bits_number"/>
        ...
        <variable name="variable_M" minimum="min_value" maximum="max_value" format="c_string_format" sweeps="sweeps_number" bits="bits_number"/>
    </calibrate>

Implemented algorithms are:

* *"sweep"*: Sweep brutal force algorithm. Requires on each variable:
> sweeps: number of sweeps to generate on each variable in every experiment. 

  The total number of simulations to run is:
> (number of experiments) x (variable 1 number of sweeps) x ... x
> (variable n number of sweeps) x (number of iterations)

* *"Monte-Carlo"*: Monte-Carlo brutal force algorithm. Requires on calibrate:
> simulations: number of simulations to run in every experiment.

  The total number of simulations to run is:
> (number of experiments) x (number of simulations) x (number of iterations)

* Both brutal force algorithms can be iterated to improve convergence by using
the following parameters:
> bests: number of bests simulations to calculate convergence interval on next
> iteration (default 1).
>
> tolerance: tolerance parameter to increase convergence interval (default 0).
>
> iterations: number of iterations (default 1).

* *"genetic"*: Genetic algorithm. Requires the following parameters:
> population: number of population.
>
> generations: number of generations.
>
> mutation: mutation ratio.
>
> reproduction: reproduction ratio.
>
> adaptation: adaptation ratio.

  and on each variable:
> bits: number of bits to encode each variable.

  The total number of simulations to run is:
> (number of experiments) x (population) x [1 + (generations - 1)
> x (mutation + reproduction + adaptation)]

SOME EXAMPLES OF INPUT FILES
----------------------------

Example 1
_________

* The simulator program name is: *pivot*
* The sintaxis is:
> $ ./pivot input_file output_file

* The program to evaluate the objective function is: *compare*
* The sintaxis is:
> $ ./compare simulated_file data_file result_file

* The calibration is performed with a *sweep brutal force algorithm*.

* The experimental data files are:
> 27-48.txt
>
> 42.txt
>
> 52.txt
>
> 100.txt

* Templates to get input files to simulator for each experiment are:
> template1.js
>
> template2.js
>
> template3.js
>
> template4.js

* The variables to calibrate, ranges, c-string format and sweeps number to perform are:
> alpha1, [179.70, 180.20], %.2lf, 5
>
> alpha2, [179.30, 179.60], %.2lf, 5
>
> random, [0.00, 0.20], %.2lf, 5
>
> boot-time, [0.0, 3.0], %.1lf, 5

* Then, the number of simulations to run is: 4x5x5x5x5=2500.

* The input file is:

_

    <?xml version="1.0"?>
    <calibrate simulator="pivot" evaluator="compare" algorithm="sweep">
        <experiment name="27-48.txt" template1="template1.js"/>
        <experiment name="42.txt" template1="template2.js"/>
        <experiment name="52.txt" template1="template3.js"/>
        <experiment name="100.txt" template1="template4.js"/>
        <variable name="alpha1" minimum="179.70" maximum="180.20" format="%.2lf" sweeps="5"/>
        <variable name="alpha2" minimum="179.30" maximum="179.60" format="%.2lf" sweeps="5"/>
        <variable name="random" minimum="0.00" maximum="0.20" format="%.2lf" sweeps="5"/>
        <variable name="boot-time" minimum="0.0" maximum="3.0" format="%.1lf" sweeps="5"/>
    </calibrate>


* A template file as *template1.js*:

_

    {
      "towers" :
      [
        {
          "length"      : 50.11,
          "velocity"    : 0.02738,
          "@variable1@" : @value1@,
          "@variable2@" : @value2@,
          "@variable3@" : @value3@,
          "@variable4@" : @value4@
        },
        {
          "length"    : 50.11,
          "velocity"  : 0.02824,
          "@variable1@" : @value1@,
          "@variable2@" : @value2@,
          "@variable3@" : @value3@,
          "@variable4@" : @value4@
        },
        {
          "length"    : 50.11,
          "velocity"  : 0.03008,
          "@variable1@" : @value1@,
          "@variable2@" : @value2@,
          "@variable3@" : @value3@,
          "@variable4@" : @value4@
        },
        {
          "length"    : 50.11,
          "velocity"  : 0.03753,
          "@variable1@" : @value1@,
          "@variable2@" : @value2@,
          "@variable3@" : @value3@,
          "@variable4@" : @value4@
        }
      ],
      "cycle-time"     : 71.0,
      "plot-time"     : 1.0,
      "comp-time-step": 0.1,
      "active-percent" : 27.48
    }

* Produce simulator input files to reproduce the experimental data file
*27-48.txt* as:

_

    {
      "towers" :
      [
        {
          "length"      : 50.11,
          "velocity"    : 0.02738,
          "alpha1" : 179.95,
          "alpha2" : 179.45,
          "random" : 0.10,
          "boot-time" : 1.5
        },
        {
          "length"    : 50.11,
          "velocity"  : 0.02824,
          "alpha1" : 179.95,
          "alpha2" : 179.45,
          "random" : 0.10,
          "boot-time" : 1.5
        },
        {
          "length"    : 50.11,
          "velocity"  : 0.03008,
          "alpha1" : 179.95,
          "alpha2" : 179.45,
          "random" : 0.10,
          "boot-time" : 1.5
        },
        {
          "length"    : 50.11,
          "velocity"  : 0.03753,
          "alpha1" : 179.95,
          "alpha2" : 179.45,
          "random" : 0.10,
          "boot-time" : 1.5
        }
      ],
      "cycle-time"     : 71.0,
      "plot-time"     : 1.0,
      "comp-time-step": 0.1,
      "active-percent" : 27.48
    }
