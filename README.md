
# geo-nepal-basins
A packaged geojson map using leaflet for rendering basins and sub basins of Nepal.
# Installation
    npm install nepal-basins-geojson
# Usage
For the basic rendering of the leaflet map with basins and sub basins geojson, create a vue component as follows:

    <script>
	    import  NepalMapBasins  from  "nepal-map-basins";
	    export default {
		    components: {
			    VueNepalBasinsGeojson,
		    },
	    }
    </script>
    
    <template>
	    <div  id="app">
		    <GeoNepalBasins  height="50"  width="50" />
	    </div>
    </template>


# Github
[https://github.com/Gritfeat-Solutions/geo-nepal-basins.git](https://github.com/Gritfeat-Solutions/geo-nepal-basins.git)
# Demo
    npm install
    npm run serve