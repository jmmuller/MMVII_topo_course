# Introduction

### MMVII

**MicMac** is a free open-source photogrammetry solution developed at (**[IGN](https://www.ign.fr/)**) - French Mapping Agency - since 2003. A second version named **MMVII** aimed at facilitating external contributions and being more maintainable in the long term has been in development since 2020.

[https://github.com/micmacIGN/micmac](https://github.com/micmacIGN/micmac)

###

In its global compensation stage, **MMVII** can handle:

  * tie points
  * ground control points (GCP)
  * distortion models
  * rigid cameras blocks
  * GNSS cameras positions (soon ?)
  * clinometers (soon ?)
  * and topometric survey measurements!


Let's call topometric survey *topo* for this presentation.





# MMVII

## Compilation
### Compilation

 - latest Windows build: https://github.com/micmacIGN/micmac/releases
 - latest documentation: https://github.com/micmacIGN/micmac/
 releases/tag/MMVII_Documentation
 - compilation instructions: https://github.com/micmacIGN/micmac/
 blob/master/MMVII/README.md


## Command line
### Command line {.fragile}

*MMVII* is mainly used with command line.

To list existing commands:

\begin{scriptsize}
\begin{verbatim}
$ MMVII
Bench => This command execute (many) self verification on MicMac-V2 behaviour
BlockCamInit => Compute initial calibration of rigid bloc cam
CERN_ImportClino => A temporary command to arrange clino format
ClinoInit => Initialisation of inclinometer
CodedTargetCheckBoardExtract => Extract coded target from images
CodedTargetCircExtract => Extract coded target from images
CodedTargetCompleteUncoded => Complete detection, with uncoded target
CodedTargetExtract => Extract coded target from images
CodedTargetGenerate => Generate images for coded target
CodedTargetGenerateEncoding => Generate en encoding for coded target, according to some specification
CodedTargetRefineCirc => Refine circ target with shape-distorsion using 3d-predict
CodedTargetSimul => Extract coded target from images
CompPIB => This command is used compute Parameter of Binary Index
Cpp11 => This command execute some test for to check my understanding of C++11
DM01DensifyRefMatch => Create dense map using a sparse one (LIDAR) with or without images
...
\end{verbatim}
\end{scriptsize}

### {.fragile}

To get the help of a command:

\begin{scriptsize}
\begin{verbatim}
$ MMVII ImportGCP
...
   => Import/Convert basic GCP file in MMVII format
 == Mandatory unnamed args : ==
  * string :: Name of Input File
  * string :: Format of file as for ex "SNASXYZSS" 
  * string [PointsMeasure,Out] :: Output PointsMeasure
 == Optional named args : ==
  * [Name=NameGCP] string :: Name of GCP set
  * [Name=NbDigName] int :: Number of digit for name, 
                 if fixed size required (only if int)
  * [Name=NumL0] int :: Num of first line to read , [Default=0]
  * [Name=NumLast] int :: Num of last line to read
                 (-1 if at end of file) ,[Default=-1]
  * [Name=PatName] std::vector<std::string> :: Pattern
                 for transforming name (first sub-expr)
  * [Name=ChSys] std::vector<std::string> :: Change
                 coordinate system, if 1 Sys In=Out,
                 [Default=[LocalNONE]]
  * [Name=MulCoord] double :: Coordinate multiplier,
                 used to change unity as meter to mm
\end{verbatim}
\end{scriptsize}

### {.fragile}

The **-help** argument displays help, whatever is already written on the command line:

\begin{scriptsize}
\begin{verbatim}
$ MMVII ImportGCP toto
Level=[UserEr:InsufP]
Mes=[Not enough Arg, expecting 3 , Got only 1]
========= ARGS OF COMMAND ==========
MMVII ImportGCP toto 
Aborted
\end{verbatim}
\end{scriptsize}


\begin{scriptsize}
\begin{verbatim}
$ MMVII ImportGCP toto -help
...
   => Import/Convert basic GCP file in MMVII format
 == Mandatory unnamed args : ==
  * string :: Name of Input File
  * string :: Format of file as for ex "SNASXYZSS" 
  * string [PointsMeasure,Out] :: Output PointsMeasure
 == Optional named args : ==
  * [Name=NameGCP] string :: Name of GCP set
...
\end{verbatim}
\end{scriptsize}

Use *-Help* for usage examples.


### {.fragile}
With the correct setup (Linux only, see *MMVII/README.md*), $<tab>$ can be used for command line completion.

Example of command line with mandatory and optional parameters:

\begin{scriptsize}
\begin{verbatim}
MMVII ImportGCP  2023-10-06_15h31PolarModule.coo  NXYZ Std  \
                 NumL0=14 NumLast=34  PatName="P\.(.*)" NbDigName=4
\end{verbatim}
\end{scriptsize}

## MMVII Projects

### MMVII Projects

A **MMVII** project root is a directory containing a set of image files or an XML file containing a list of image files names (*SetOfName*).

**MMVII** will write and read data in a directory named *MMVII-PhgrProj* that will be created automatically when needed.

The file structure is as follows:

###

~~~~~~~
 
|-- *.JPG                     <-- image files
|-- MMVII-LogFile.txt
+-- MMVII-PhgrProj/
    +-- InitialOrientations
    +-- MetaData              <-- metadata rules
    |   +-- Std
    +-- Ori                   <-- calib and img ori
    |   +-- InitL93
    |   +-- InitRTL
    |   +-- FinalRTL
    +-- PointsMeasure         <-- 3d and 2d coords
    |   +-- InitL93
    |   +-- InitRTL
    +-- Reports
    +-- RigBlock
    +-- SysCo
    +-- Topo
~~~~~~~

###

Example:

With initial orientation files *MMVII-PhgrProj/Ori/Init/\*.xml*,
just give *Init* as command line argument.





# SysCo

### Introduction
Coordinate systems (SysCo) types supported by MMVII are:

 * **Local**: any Euclidian frame, without any geolocalization or vertical direction knowledge
 * **GeoC**: geocentric coordinates
 * **RTL**: a local Euclidian frame defined by an origin point where Z is normal to ellipsoid
and X is on East direction
 * **Proj**: any georeferenced system supported by the PROJ library

When SysCo is known, its definition is recorded into the file CurSysCo.xml, in Ori and PointsMeasure directories.


### SysCo definition
The SysCo definitions for MMVII commands can be:

* the name of a file in MMVII source subfolder *MMVII/MMVII-RessourceDir/SysCo* or in project subfolder *MMVII-PhgrProj/SysCo*, without its extension (e.g., *L93*)
* any PROJ definition (e.g., *EPSG:4326*)
* any string starting with **Local** for a local frame (e.g., LocalAMRules)
* **GeoC** for a geocentric frame

### SysCo definition

* a string starting with **RTL**, with the pattern: *RTL\*X0\*Y0\*Z0\*Def* (e.g., *RTL\*0.675\*45.189\*0\*EPSG:4326*), where you give the origin point coordinates in a certain PROJ system.

  \begin{center}
        \includegraphics[height = 5cm]{img/cart_geocentr.png}
  \end{center}

### Examples

* **SysCo=L93** will set the SysCo to *IGNF:LAMB93*, as defined in *MMVII/MMVII-RessourceDir/SysCo/L93.xml*
* **SysCo=IGNF:LAMB1** will set the SysCo to Lambert I
* **SysCo=LocalPanel** will set the SysCo to a local frame defined as ”LocalPanel”, that will not be convertible into any other SysCo
* **SysCo=RTL\*657700\*6860700\*0\*IGNF:LAMB93** will set the SysCo to a tangent local Euclidian frame, with origin (657700, 6860700, 0) in Lambert 93
* **SysCo=GeoC** will set the SysCo to geocentric coordinates


C.F doc chapter 21.


# Topo

C.F doc chapter 22.

## Principles

### Station

Topo measurements are made from an instrument that is verticalized/plumb or not.
The position and orientation of an instrument define a \textit{station}.
All the measurements are attached to a station and are expressed in the station frame.

### 
\begin{figure}[!h]
\centering
\includegraphics[width=10cm,trim={0 0.5cm 0 0},clip]{../CommandReferences/ImagesComRef/topo.png}
\end{figure}

### Measurements

The following measurements types are currently supported:

  * distances
  * horizontal angles
  * zenithal angles
  * direct Euclidian vector observation

The measurements can be made between cameras poses, GCPs or undeclared points that will be inserted into the last GCP set of the adjustment.


### Usage


Two MMVII commands can use topo measurements in compensation:

 * *OriBundleAdj* via the *TopoFile* option
 * *TopoAdj*: when there is no photo


The topo measurements files can be given as a set of MMVII json or xml files, or in a simplified text format (named *OBS* file) inherited from IGN's
Comp3D micro-geodesy compensation software.

All the measurements files must be in the *MMVII-PhgrProj/Topo/[TopoObsName]* folder.

## OBS format

### OBS format
MMVII supports only a subset of Comp3D *OBS* format (https://ignf.github.io/Comp3D/doc/obs.html).

*OBS* files are text files with fields delimited by any number of spaces or tabs. Blank lines are overlooked.
The * character defines a comment that goes up to the end of the line.

###
A measurement line is composed by:

 * code: an integer representing the type of observation
 * station name
 * target name
 * measurement value (meters for distances, gon for angles)
 * measurement *a priori* $\sigma$ (meters for distances, gon for angles)
 * anything else is ignored until the end of the line


Example of an *OBS* line describing a measured distance of 100.0000 m, with a $\sigma$ of 1 mm from *PointA* to *PointB*:


    3  PointA   PointB   100.0000   0.001  * comment

###
The observations codes are:


  *  *3*: 3D distance
  *  *5*: local horizontal (hz) angle
  *  *6*: local zenithal (zen) angle
  *  *7*: local horizontal angle for a new station 
  *  *14*: local $\Delta$x
  *  *15*: local $\Delta$y
  *  *16*: local $\Delta$z

# Example 1

###
An example of topo dataset can be found in *MMVII/MMVII-UseCaseDataSet/TopoMini/*.
It corresponds to this configuration:

\begin{figure}[!h]
\centering
\includegraphics[width=9cm]{../CommandReferences/ImagesComRef/topo2.png}
\end{figure}

### 3D points file
The initial coordinates of the 4 points, in Lambert 93, are in a simple text file (*inputs/coords.txt*):

    * 1st column:  0 = free point
    1  PtA  657700.000  6860700.000  10.000
    0  PtB  657710      6860700      10   * approx
    0  PtC  657710      6860710      10   * approx
    1  PtD  657700.000  6860690.000  10.000

The coordinates of PtA and PtD are supposed known (with a certain precision).
The coordinates of PtB and PtC are just for initialization.

How to import this using the *ImportGCP* command?

###

We give the text format (additional\_info, name, x, y, z), the name of the resulting *PointsMeasure* and the coordinates SysCo.

We also specify that the points that have '0' for their additional\_info are free points, that the sigma for
known points is 0.001m and that lines starting with '*' are comment lines.


    MMVII ImportGCP inputs/coords.txt ANXYZ InitL93 \
      ChSys=[L93] AddInfoFree=0 Sigma=0.001 Comment=*


In the resulting file *MMVII-PhgrProj/PointsMeasure/InitL93/MesGCP-coords.xml*,
the points PtA and PtD have a set *\_\_Opt\_\_Sigma2* equivalent to $\sigma = 0.001 m$,
the points PtB and PtC have no *\_\_Opt\_\_Sigma2*, making them free points.

### SysCo
A *RTL* SysCo is mandatory to be able to compute a topo compensation.
PtA is chosen as RTL origin (tangency point).
The SysCo definition is:

    RTL*657700*6860700*0*IGNF:LAMB93

The *GCPChSysCo* command does the conversion to *RTL*:


    MMVII GCPChSysCo "RTL*657700*6860700*0*IGNF:LAMB93" \
      InitL93 InitRTL

### Measurements {.fragile}

 * an instrument on PtA measures hz angle, zen angle and distance to PtB and PtC
 * an instrument on PtD make the same to PtB and PtC


The corresponding *OBS* file (*inputs/meas.obs}* is:

\begin{scriptsize}
\begin{verbatim}
 7   PtA    PtB     0      0.001
 6   PtA    PtB   100      0.001
 3   PtA    PtB    10.05   0.005
 5   PtA    PtC   -40.62   0.001
 6   PtA    PtC   100      0.001
 3   PtA    PtC    14.88   0.005

 7   PtD    PtB     0      0.001
 6   PtD    PtB   100      0.001
 3   PtD    PtB    14.88   0.005
 5   PtD    PtC   -14.96   0.001
 6   PtD    PtC   100      0.001
 3   PtD    PtC    22.82   0.005
\end{verbatim}
\end{scriptsize}

### Measurements

This file has to be copied into a subdirectory of *MMVII-PhgrProj/Topo*:

    mkdir -p MMVII-PhgrProj/Topo/Obs1/
    cp inputs/meas.obs MMVII-PhgrProj/Topo/Obs1/


### Unknowns count

Two verticalized stations, one with its origin on PtA, the other on PtD.
Each has an horizontal orientation unknown ($G_0$) due to the random orientation of the instrument
when it has been set.

The number of unknowns in this configuration is:

   * 3 per point ($x, y, z$)  $\rightarrow$ 12 unknowns
   * 1 per station ($G_0$)  $\rightarrow$ 2 unknowns

The number of constraints is:

 * 3 per constrained point, PtA and PtD  $\rightarrow$ 6 constraints
 * 1 per topo measurement $\rightarrow$ 12 constraints


Total: 14 unknowns, 18 constraints.



### Adjustment {.fragile}

The *TopoAdj* command can perform an adjustment between topo and GCP constraints.
It is used as a substitute to *OriBundleAdj* when there are no cameras.

\begin{scriptsize}
\begin{verbatim}
 == Mandatory unnamed args : ==
  * string [Topo,In] :: Dir for Topo measures
  * string [Topo,Out] :: Dir for Topo measures output
  * string [PointsMeasure,In] :: Dir for points initial coordinates
  * string [PointsMeasure,Out] :: Dir for points final coordinates

 == Optional named args : ==
  * [Name=GCPW] double :: Constrained GCP weight factor (default: 1)
  * [Name=DataDir] string :: Default data directories  ,[Default=Std]
  * [Name=NbIter] int :: Number of iterations ,[Default=10]
  * [Name=GCPFilter] string :: Pattern to filter GCP by name
  * [Name=GCPFilterAdd] string :: Pattern to filter GCP by additional info
  * [Name=GCPDirOut] string [PointsMeasure,Out] :: Dir for output GCP
  * [Name=LVM] double :: Levenberg–Marquardt parameter (to have better conditioning
                         of least squares) ,[Default=0]
\end{verbatim}
\end{scriptsize}

###

In our example, the input topo directory is *Obs1* and the input PointsMeasure is *InitRTL*.
We give output directories names for topo and points.

    MMVII TopoAdj Obs1 Obs1_out InitRTL FinalRTL

The final $\sigma_0$ value should be around 1 if everything goes well.
In this example, $\sigma_{0 init} > 5000$, because the initial coordinates of PtB and PtC are approximate,
and after 10 iterations it stabilizes at $\sigma_{0 final} = 1.7$.

### Outputs

The output topo directory contains a single xml file with all the measurements and some output values (residuals,
stations orientations...). It can be used as topo input file.


The last step is to convert the RTL coordinates to Lambert 93:

    MMVII GCPChSysCo L93 FinalRTL FinalL93

# Stations

### Stations orientation constraints

Each station has orientation constraints that have to be given before the station observations lines in the *OBS* file.

The possible orientation constraints are:

   * \texttt{\#FIX}: the station is axis-aligned, it is verticalized and oriented to North
   * \texttt{\#VERT}: the station is verticalized and only horizontal orientation is free
   * \texttt{\#BASC}: the station orientation has 3 degrees of freedom, meaning non-verticalized and not oriented to North

### 
After a \texttt{\#} line (\texttt{\#FIX}, \texttt{\#VERT} or \texttt{\#BASC}), all the following stations have the new orientation constraint until the next \texttt{\#} line.

Each \texttt{OBS} file starts with an implicit \texttt{\#VERT}, making the stations verticalized by default.

For now, the vertical is modeled as the Earth's ellipsoid normal. Vertical deflection grids may be added later.

### Example {.fragile}
It is possible to say that the station on PtA was not verticalized by using \texttt{\#BASC}:

\begin{scriptsize}
\begin{verbatim}
#BASC
 7   PtA    PtB     0      0.001
 6   PtA    PtB   100      0.001
 3   PtA    PtB    10.05   0.005
 5   PtA    PtC   -40.62   0.001
 6   PtA    PtC   100      0.001
 3   PtA    PtC    14.88   0.005

#VERT
 7   PtD    PtB     0      0.001
 6   PtD    PtB   100      0.001
 3   PtD    PtB    14.88   0.005
 5   PtD    PtC   -14.96   0.001
 6   PtD    PtC   100      0.001
 3   PtD    PtC    22.82   0.005
\end{verbatim}
\end{scriptsize}


### Several stations on the same point

MMVII automatically creates a new station when a point is used for the first time as the origin of a measurement.

If we have to make a new set of orientation unknowns because two instruments were set on the same point with different
orientations, we can:

   * use separated \texttt{OBS} files
   * add a \texttt{\#} line to separate the measurements sets
   * use a code \textbf{7} for the first measurement

If a station only has distances measurements, it is automatically set as a \texttt{\#FIX} station, since this station orientation unknowns can't be estimated.


# Example 2

## Principle
### Principle

*TopoPrissma9img* dataset: find a car trajectory from fixed cameras

\begin{figure}[!h]
\centering
\includegraphics[width=10cm]{img/ex2.jpg}
\end{figure}

Each camera is pre-calibrated.


###

There are several fixed ground points used to impose an orientation to the cameras:

\begin{figure}[!h]
\centering
\includegraphics[width=8cm]{img/ex2_map.jpg}
\end{figure}


###

The car is equipped with several coded targets.
The 3D coordinates of the targets in the car frame were measured by topometry.

\begin{figure}[!h]
\centering
\includegraphics[width=8cm]{img/ex2_car.jpg}
\end{figure}


## Cameras orientation
### Cameras orientation

To get the cameras orientation:

 * import ground targets 3D coordinates
 * import ground targets images coordinates
 * add images metadata to make calibration/image links
 * import cameras calibrations
 * cameras initial resection on ground targets
 * adjustment on ground targets


### Ground targets

    # get 3d coords for ground targets, convert to a RTL
    MMVII ImportGCP inputs/coord_gnd.cor SNXYZ GND \
       ChSys=[L93,'RTL*644000*6836370*0*IGNF:LAMB93'] \
       Sigma=0.001

    # make SysCo shortcut 'RTL'
    cp MMVII-PhgrProj/PointsMeasure/GND/CurSysCo.xml \
       MMVII-PhgrProj/SysCo/RTL.xml

    # copy 2d mes for gnd points
    cp inputs/gnd_img_V2/* \
       MMVII-PhgrProj/PointsMeasure/GND/

### Image Metadata

    # set metadata
    MMVII EditCalcMTDI Std ModelCam \
       Modif=['IGN2_.*.jpg',BFS-PGE-161S7M-C,0] Save=1
    MMVII EditCalcMTDI Std AdditionalName \
       Modif=['IGN2_.*cam-2234.(...)-.*.jpg','$1',0] \
       Save=1

    # focal 8mm for 125 130 138, the others 12mm
    MMVII EditCalcMTDI Std Focalmm =\
       Modif=['IGN2_.*-cam-223..1(25|30|38)-.*.jpg',8,0]\
       Save=1
    MMVII EditCalcMTDI Std Focalmm \
       Modif=['IGN2_.*-cam-.*.jpg',12,1] Save=1

### Cameras orientation

    # copy calibs
    mkdir -p MMVII-PhgrProj/Ori/Calib
    cp inputs/calibsV2/*.xml MMVII-PhgrProj/Ori/Calib

    # initial orientation on GND points
    MMVII OriPoseEstimSpaceResection ".*.jpg" GND \
        Calib Init

    # adjust ori on gnd points
    MMVII OriBundleAdj ".*.jpg" Init Adjusted \
        GCPDir=GND GCPW=[1,0.5] PPFzCal=".*" \
        PoseVisc=[1,1]
    
    MMVII ReportGCP ".*.jpg" GND Adjusted

## Car frame
### Car frame

To get the car origin from the images :

* fix cameras orientation, position and calibration
* get the coded targets images coordinates
* use the targets coordinates in car sub-frame (as topo obs)
* adjust


### Coded targets

    # extract coded targets
    MMVII CodedTargetExtract 'IGN2_.*.jpg' \
       inputs/IGNDroneSym_*_FullSpecif.xml \
       Adjust=1 CC=1 DMD=16 Debug=511 \
       OutPointsMeasure=Targets
    # results can be checked in MMVII-PhgrProj/VISU/


\begin{figure}
\includegraphics[width=7cm]{img/ex2_targets}
\end{figure}


### Prepare topo  {.fragile}

The OBS file *inputs/car_xyz.obs* represents the car frame
as a set of local euclidian measurements:

\begin{scriptsize}
\begin{verbatim}
14 car 00 -0.7180 0.0003 * DX from car to 00 = -0.7180, sigma = 0.0003
15 car 00 -0.7048 0.0003 * DY from car to 00
16 car 00 0.8287 0.0003  * DZ from car to 00
14 car 01 0.6973 0.0003
15 car 01 -0.7168 0.0003
16 car 01 0.8470 0.0003
...
\end{verbatim}
\end{scriptsize}

### Prepare topo

    # get approximate 3d coords for coded targets,
    # as free points
    MMVII ImportGCP inputs/coord_approx_car.cor \
       SNXYZ Targets ChSys=[L93,RTL] Sigma=-1

    #import topo: coded targets coords in car frame
    mkdir -p MMVII-PhgrProj/Topo/BlocCar/
    cp inputs/car_xyz.obs MMVII-PhgrProj/Topo/BlocCar/

### Adjust car frame
    # adjust with frozen cameras
    MMVII OriBundleAdj ".*.jpg" Adjusted Out \
       GCPDir=Targets GCPW=[1,0.1] TopoDirIn=BlocCar \
       PPFzCal=".*" PatFzCenters=".*" PatFzOrient=".*" \
       TopoDirOut=BlocCarOut GCPDirOut=CarOut NbIter=20

    # export coords to L93
    MMVII GCPChSysCo L93 CarOut CarOutL93

Check :

 * *MMVII-PhgrProj/Topo/BlocCarOut/TopoOut.xml* for topo residuals
 * *MMVII-PhgrProj/PointsMeasure/CarOutL93/MesGCP-NewGCP.xml* for final coordinates.

### Adjust car frame

Use both GCP sets at the same time with AddGCPW:

    MMVII OriBundleAdj ".*.jpg" Adjusted Out2
       GCPDir=GND GCPW=[1,0.5] \
       AddGCPW=[[Targets,1,0.1]] TopoDirIn=BlocCar \
       PPFzCal=".*" PatFzCenters=".*" PatFzOrient=".*" \
       TopoDirOut=BlocCarOut GCPDirOut=CarOut NbIter=20

or:

    MMVII OriBundleAdj ".*.jpg" Adjusted Out2 \
       GCPDir=Targets GCPW=[1,0.1] \
       AddGCPW=[[GND,1,0.5]] TopoDirIn=BlocCar \
       PPFzCal=".*" PatFzCenters=".*" PatFzOrient=".*" \
       TopoDirOut=BlocCarOut GCPDirOut=CarOut NbIter=20

# Implementation

###

Doc 12.8

Formulas
Operators (diffangmod)




# Direct Dev

## Code 8

## Code 4

## Some initializations


