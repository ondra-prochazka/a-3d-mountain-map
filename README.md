# A 3D Mountain Map of the Earth

This repository holds configuration files, installation instructions, and
live GitHub pages for my [3D global mountain map][1] website.

The website is intended as a simple demonstration of how a global
interactive 3D map of Earth mountains, adhering to the time-proven rules of
cartographic design may be built with open-source technology and freely
available data.

The website has been created to accompany my talk "Visual Hierarchies in 3D
cartography With VTS", first held at FOSS4G-NA 2019 in San Diego in April
2019.

You are invited to clone this repository and use it as a base for your own 3D
mapping projects. To recreate the original mountain map, simply follow the
instructions below, or fork the repository and do the same.

## Using This Repository to Run Your Own 3D Mountain Map

### Prerequisites 

You will need a functioning [vts-geospatial][2] backend. The easiest way to do this is to
use Melowntech's public repositories for Ubuntu 18.04 LTS.

First, set up the repository on your system:

```
    $ sudo apt install apt-transport-https
    $ cd /etc/apt/sources.list.d/ && sudo wget http://cdn.melown.com/packages/conf/melown-bionic.list
    $ wget -O - http://cdn.melown.com/packages/keys/oss.packages%40melown.com.key | sudo apt-key add -
```

And install VTS backend:

```
    $ sudo apt-get update
    $ sudo apt-get install vts-backend
```

The remainder of this tutorial presumes that you have set up your Vtsgeo
backend in this way - if you choose another way, such as manually installing
Vtsgeo from sources, you need to change some of the paths according to your
installation.

Perform any of the bellow steps as the vts system user:

```
    # sudo -iu vts
```

### Cloning the repository 

Clone the repository into vts user home directory

```
    $ mkdir -p ~/git && cd ~/git
    $ git clone https://github.com/ondra-prochazka/a-3d-mountain-map-of-the-earth.git
```

and create some symlinks to the project's storage views and resource
configuration files:

```
    $ cd ~/git/a-3d-mountain-map-of-the-earth
    $ mkdir -p ~/store/a-3d-mountain-map
    $ ln -s $PWD/var_vts_store_a-3d-mountain-map_stylesheet ~/store/a-3d-mountain-map/stylesheet
    $ ln -s $PWD/var_vts_store_a-3d-mountain-map_map-config ~/store/a-3d-mountain-map/map-config
    $ ln -s $PWD/etc_vts_mapproxy_a-3d-mountain-map_d /etc/vts/mapproxy/a-3d-mountain-map.d
    $ ln -s $PWD/var_vts_mapproxy_datasets_a-3d-mountain-map /var/vts/mapproxy/datasets/a-3d-mountain-map
```

### Setting up tileset storage

Add global Viewfinder Panoramas DEM to vtsd storage:

```
    $ vts ~/store/stage.melown2015 –add \ 	          
       //cdn.melown.com/vts/melown2015/terrain/global/viewfinder3/ --top
```

### Setting up the local resources

Make the directory to hold the local resources:

```
    $ mkdir ~/mapproxy/datasets/a-3d-mountain-map
```

Download the peaklist.org's list of ultra-prominent peaks (also, read the
terms of use for this dataset):

```
    $ wget http://cdn.melown.com/pub/vts-tutorials/high-terrain/world1500.json \
        -O ~/mapproxy/datasets/a-3d-mountain-map/world1500.json 
```


### Configuring the backend 

Make vts-mapproxy aware of the project's resources. Add the following line to
`/etc/vts/mapproxy/resources.json` (before the closing bracket):

```
    , { "include": "a-3d-mountain-map.d/*.json" }
```

and make the server take notice (as root):

```
    $ /etc/init.d/vts-backend-mapproxy force-update    
```

Finally, make a subtle change to vts-vtsd config file, to make it handle the project's stylesheets properly.
Open `/etc/vts/vtsd/vtsd.conf`, find this snippet:

```
    # Disable dataset handling for stylesheets
    #[location</stylesheet>]
    [location<stylesheet>]
```

and modify it as follows:

```
    # Disable dataset handling for stylesheets
    #[location</stylesheet>]
    [location<stylesheet>]
```

That is it! Point your browser to http://&lt;your-server&gt;:8070/store/a-3d-mountain-map/map
and enjoy the beauty of Earth's landforms. 

![a-3d-mountain-map-initial-view][4]


## Legal notice

Please respect any license restrictions applicable to the data sources used
in this demonstration. Respective copyright holders are clearly marked in the
bottom-right area of the map window:

![Credits section in vts-browser-js][3]


[1]: https://ondra-prochazka.github.io/a-3d-mountain-map-of-the-earth
[2]: https://vtsdocs.melown.com/
[3]: ../master/media/credits.png
[4]: ../master/media/a-3d-mountain-map.jpg

