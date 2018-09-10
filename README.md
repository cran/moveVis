# moveVis

## Introduction

This is an R package providing tools to visualize movement data by creating path animations from geo-location point data. The package is under ongoing development.  The moveVis package is working hand in hand with the move package by using the move and moveStack class and the raster package. It is based on a ggplot2 plotting architecture and relys on the libraries ImageMagick, ffmpeg and libav. To be informed about updates, new features and the current version, visit [news.movevis.org](http://news.movevis.org).

## Installation

This is the official moveVis R package repository, including beta code versions before submitted to CRAN. For operational use of moveVis, please use the current stable CRAN version of moveVis.

To install stable version from CRAN, please execute:

```s
install.packages('moveVis')
```

To install the development version from this GitHub repository, please execute:

```s
devtools::install_github("16EAGLE/moveVis")
```

## Getting started

You can use moveVis with any move or moveStack object. This guide shortly explains how to prepare your own geo-location point data for the animate_move() function by creating a move class object from a data.frame class object. As an example, the provided example data (data.frame) are used. Instead, you could use any similar prepared data of yours.

### External libraries

moveVis requires at least one of the three external libraries 'ffmpeg', 'libav' and/or 'ImageMagick'. They support different types of output formats (gif, mov, mp4 etc.). If you have 'ImageMagick' and either 'ffmepg' or 'libav' installed, you can use all output formats supported by moveVis.

Run `get_libraries()` to find out, which libraries are installed on your system:

```s
get_libraries()
```

#### Linux

On many Linux distributions, ImageMagick and FFmpeg are preinstalled or can be installed via the package manager. On Ubuntu, use `sudo apt-get install imagemagick` to install ImageMagick containing the `convert` tool and `sudo apt-get install ffmpeg` to install FFmpeg containing the equally called `ffmpeg` tool.

#### Windows

You can download and install both ImageMagick and FFmpeg from https://www.imagemagick.org/script/download.php as binary for Windows. Make sure that you select "Install FFmpeg" and "Install legacy utilities (e.g. convert)" during the installation process. `get_libraries` will recognize both libraries only, if their commands can be called from the command line. Make sure that you select the related option during the installation process. 

#### maxOS/OS X

Visit https://www.macports.org/install.php and follow the instructions there to download and install MacPorts. Execute `sudo port install ImageMagick` to install the latest binary release of ImageMagick for Mac. Visit https://www.ffmpeg.org/download.html to download and install the latest binary release of FFmpeg for Mac.

### Load moveVis

After the required libraries are once installed, <b>restart your R session</b>. Then, load moveVis and the move package:

```s
#Load packages
library(moveVis)
library(move)
```

and run `get_libraries()` to check, if the  installed tools are recognized by moveVis. If so, everything is set for starting to create your first moveVis animation. If the installed tools are not recognized automatically, you can provide the paths to the tools manually (see argument `conv_dir` in the manual of `animate_move()`)

### Data preperation

You will need to load the example data for this tutorial:

```s
#Load data (data.frame) (or use your own as data.frame)
data("move_data")
```

As the provided example data, your data.frame needs to have at least three columns: two columns for your coordinates (here "lat", "lon") and one for the date/time stamp (here "dt"). The date/time stamps need to be converted to POSIXct as follows:

```s
move_data$dt <- as.POSIXct(strptime(move_data$dt, "%Y-%m-%d %H:%M:%S", tz = "UTC"))
```

Your movement data need to be provided as move class objects to the animate_move() function. For each individual movement path you want to display simultaniously within a single animation, you will need one move class object. The move class objects per path are provided as a list. If your data.frame contains several individuals (e. g. differentiable by a "individuals" column, as the example data.frame does), then subset the data per individual and store the namings. If you just want to display a single path, you do not have to do this.

```s
#Create new move class object list by individual
data_ani <- split(move(move_data$lon, move_data$lat, proj=CRS("+proj=longlat +ellps=WGS84"),
                       time = move_data$dt, animal=move_data$individual, data=move_data))
```

### Additional arguments

get_libraries() returns the library commands that are needed by the animate functions. Just save them to a variable that you can later pass to the animate function. You can also call get_formats() to see all output formats, you can choose from.


```s
#Get libraries 
conv_dir <- get_libraries()

#Find out, which output file formats can be used
get_formats()

```

Last, you need to specify the output directory path and you can specify some optional variables such as the animation title (for details on all the arguments of animate_move() , read the animate_move() help).

```s
#Specify output directory
out_dir <- paste0(getwd(),"/test")

#Specify some optional appearance variables
img_title <- "Movement of the white stork population at Lake Constance, Germany"
img_sub <- paste0("including individuals ",indi_names)
img_caption <- "Projection: Geographical, WGS84; Sources: Movebank 2013; Google Maps"
```

### Animate function call

Finally, you are now prepared to call animate_move(), which will have to work for a while depending on your input. Here, for demonstrational purposes, we use `frames_nmax` set to 50 to force the function to only produce 50 frames and then finish the animation, regardless how many input points you provided. Set `log_level` to 1 to be informed of anything the function is doing. Set `out_format` to "mov" to get a .mov video output file.

```s
#Call animate_move()
animate_move(data_ani, out_dir, conv_dir = conv_dir, tail_elements = 10,
             paths_mode = "true_data", frames_nmax = 50,
             img_caption = img_caption, img_title = img_title,
             img_sub = img_sub, log_level = 1, out_format = "mov")
```

After the function is finished, check the output directory. Retry everything with different settings and modes, described in the function manuals. Further examples and explanations are provided within the function manuals.

## Contact & bug reports

moveVis is being developed and maintained by Jakob Schwalb-Willmann. For bug reports, please use <https://github.com/16eagle/movevis/issues> to contact me. Feature requests and other contributions are also welcome.

## What else are we doing?

The Department of Remote Sensing of the University of Würzburg has developed other R packages that might interest you:
 * <a target="_blank" href="http://bleutner.github.io/RStoolbox/">RStoolbox</a>
 * <a target="_blank" href="https://github.com/RRemelgado/rsMove/">rsMove</a>

For other news on the work at at the Department of Remote Sensing of the University of Würzburg, click <a target="_blank" href="http://remote-sensing.eu/">here</a>.


## Acknowledgements
          
This initiative is part of the Opt4Environment project and was funded by the German Aerospace Center (DLR) on behalf of the Federal Ministry for Economic Affairs and Energy (BMWi) with the research grant <b>50 EE 1403</b>.
