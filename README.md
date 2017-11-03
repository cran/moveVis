# moveVis

## Introduction

This is an R package providing tools to visualize movement data by creating path animations from geo-location point data. The package is under ongoing development.  The moveVis package is working hand in hand with the move package by using the move and moveStack class and the raster package. It is based on a ggplot2 plotting architecture and relys on the libraries ImageMagick, ffmpeg and libav.

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

You can use moveVis with any move or moveStack object. This guide shortly explains how to prepare your own geo-location point data for the animate_move() function by creating a move class object from a data.frame class object. As an example, the provided example data (data.frame) are used. Instead, you could use any similar prepared data of yours. First, you will need to load the move and the moveVis package and possibly the example data:

```s
#Load packages
library(move)
library(moveVis)

#Load data (data.frame) (or use your own as data.frame)
data("move_data")
```

As the provided example data, your data.frame needs to have at least three columns: two columns for your coordinates (here "lat", "lon") and one for the date/time stamp (here "dt"). The date/time stamps need to be converted to POSIXct as follows:

```s
move_data$dt <- as.POSIXct(strptime(move_data$dt, "\%Y-\%m-\%d \%H:\%M:\%S", tz = "UTC"))
```

Your movement data need to be provided as move class objects to the animate_move() function. For each individual movement path you want to display simultaniously within a single animation, you will need one move class object. The move class objects per path are provided as a list. If your data.frame contains several individuals (e. g. differentiable by a "individuals" column, as the example data.frame does), then subset the data per individual and store the namings. If you just want to display a single path, you do not have to do this.

```s
#Create new move class object list by individual
data_ani <- split(move(move_data$lon, move_data$lat, proj=CRS("+proj=longlat +ellps=WGS84"),
                       time = move_data$dt, animal=move_data$individual, data=move_data))
```

Please note that the animate_move() function needs at least one of the three external libraries 'ffmpeg', 'libav' and/or 'ImageMagick'. They support different types of output formats (gif, mov, mp4 etc.). If you have them all installed, you can use all output formats supported by moveVis. Run get_libraries() to find out, which libraries are installed on your system, to download and to install the needed libraries. get_libraries() returns the library commands that are needed by the animate functions.

```s
#Get libraries 
conv_dir <- get_libraries()

#Find out, which output file formats can be used
get_formats()

```

Last, you need to specify the output directory path and you can specify some optional variables such as the animation title (for details on all the arguments of animate_move() , read the animate_move() help).

```s
#Specify output directory
out_dir <- "/your/full/output/directory"

#Specify some optional appearance variables
img_title <- "Movement of the white stork population at Lake Constance, Germany"
img_sub <- paste0("including individuals ",indi_names)
img_caption <- "Projection: Geographical, WGS84; Sources: Movebank 2013; Google Maps"
```

Finally, you are now prepared to call animate_move(), which will have to work for a while depending on your input. Here, we use "frames_nmax" set to 50 to force the function to only produce 50 frames and then finish the GIF, regardless how many  input points you provided. Set "log_level" to 1 to be informed of anything the function is doing.

```s
#Call animate_move()
animate_move(data_ani, out_dir, conv_dir = conv_dir, tail_elements = 10,
             paths_mode = "true_data", frames_nmax = 50,
             img_caption = img_caption, img_title = img_title,
             img_sub = img_sub, log_level = 1, out_format = "mov")
```

Further examples and explanations on different modes are provided within the function manuals.

## Contact & bug reports

moveVis is being developed and maintained by Jakob Schwalb-Willmann. For bug reports, please use <https://github.com/16eagle/movevis/issues> to contact me. Feature requests and other contributions are also welcome.

## What else are we doing?

The Department of Remote Sensing of the University of Würzburg has developed other R packages that might interest you:
 * <a target="_blank" href="http://bleutner.github.io/RStoolbox/">RStoolbox</a>
 * <a target="_blank" href="https://github.com/RRemelgado/rsMove/">rsMove</a>

For other news on the work at at the Department of Remote Sensing of the University of Würzburg, click <a target="_blank" href="http://remote-sensing.eu/">here</a>.


## Aknowledgements
          
This initiative is part of the <a target="_blank" href="http://www.fernerkundung.geographie.uni-wuerzburg.de/forschung/projekte/laufende_projekte/opt4environment">Opt4Environment</a> project and was funded by the German Aerospace Center (DLR) on behalf of the Federal Ministry for Economic Affairs and Energy (BMWi) with the research grant <b>50 EE 1403</b>.