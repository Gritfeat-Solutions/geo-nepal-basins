<template>
  <link
    href="https://fonts.googleapis.com/css2?family=Material+Icons"
    rel="stylesheet"
  />
  <div id="map" :style="{ height: height + 'vh', width: width + 'vw' }">
    <button @click="goBack()" class="back-btn">
      <span class="material-icons"> arrow_back_ios </span>
    </button>
  </div>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";
export default {
  name: "App",
  props: {
    height: {
      type: Number,
      default: 98,
      required: false,
    },
    width: {
      type: Number,
      default: 98,
      required: false,
    },
  },
  components: {},
  data() {
    return {
      basinsMap: null,
      basinGeoJson: null,
      subBasinGeoJson: null,
      baseUrl: "https://d2uw83jaqkprs0.cloudfront.net",
      currentBasin: null,
      currentSubBasin: null,
    };
  },
  async mounted() {
    this.basinsMap = L.map("map", {
      scrollWheelZoom: false,
      touchZoom: false,
      doubleClickZoom: false,
    });
    L.tileLayer(
      "https://cartodb-basemaps-{s}.global.ssl.fastly.net/rastertiles/voyager/{z}/{x}/{y}.png"
    ).addTo(this.basinsMap);

    const res = await fetch(this.baseUrl + "/assets/scripts/Basins.json");
    const data = await res.json();

    this.basinGeoJson = L.geoJson(data, {
      style: this.style,
      onEachFeature: this.onEachFeature,
    }).addTo(this.basinsMap);
    this.basinsMap.fitBounds(this.basinGeoJson.getBounds());
  },
  async created() {},
  methods: {
    style() {
      return {
        weight: 2,
        opacity: 1,
        color: "#fff",
        dashArray: "1",
        fillOpacity: 0.7,
        fillColor: "#87ceeb",
      };
    },
    styleSubBasin() {
      return {
        weight: 2,
        opacity: 1,
        color: "#FFF",
        dashArray: "1",
        fillOpacity: 0.7,
        fillColor: "LightGreen",
      };
    },

    highlightFeature(e) {
      var layer = e.target;
      layer.setStyle({
        weight: 2,
        color: "black",
        dashArray: "",
        fillOpacity: 0.7,
        fillColor: "#fff",
      });
      if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
        layer.bringToFront();
      }
    },
    resetHighlight(e) {
      var layer = e.target;
      layer.setStyle(this.style());
    },
    resetHighlightSubBasin(e) {
      e.target.setStyle(this.styleSubBasin());
    },

    async addSubBasins(e) {
      this.currentBasin = e.target;
      if (this.subBasinGeoJson != undefined) {
        this.subBasinGeoJson.clearLayers();
      }
      const basinProperties = e.target.feature.properties;
      const res = await fetch(
        `${this.baseUrl}/assets/scripts/${basinProperties.code}.json`
      );
      const data = await res.json();
      this.subBasinGeoJson = L.geoJson(data, {
        style: this.styleSubBasin,
        onEachFeature: this.onEachSubBasinFeature,
      }).addTo(this.basinsMap);
      await this.zoomToArea(e);

      this.subBasinGeoJson.eachLayer(function (layer) {
        layer
          .bindTooltip(layer.feature.properties?.basin, {
            permanent: true,
            direction: "center",
          })
          .openTooltip();
      });
    },
    zoomToArea(e) {
      this.currentSubBasin = e.target;
      this.basinsMap.fitBounds(e.target.getBounds());
    },
    onEachSubBasinFeature(feature, layer) {
      layer.on({
        mouseover: this.highlightFeature,
        mouseout: this.resetHighlightSubBasin,
        click: this.zoomToArea,
      });
    },
    onEachFeature(feature, layer) {
      layer.on({
        mouseover: this.highlightFeature,
        mouseout: this.resetHighlight,
        click: this.addSubBasins,
      });
    },
    goBack() {
      if (this.currentBasin) {
        if (this.currentSubBasin) {
          if (
            this.currentBasin?.feature?.properties ===
            this.currentSubBasin?.feature?.properties
          ) {
            this.basinsMap.removeLayer(this.subBasinGeoJson);
            this.basinsMap.fitBounds(this.basinGeoJson.getBounds());
            this.currentBasin = this.currentSubBasin = null;
          } else {
            this.basinsMap.fitBounds(this.currentBasin.getBounds());
            this.currentSubBasin = null;
          }
        } else {
          this.basinsMap.removeLayer(this.subBasinGeoJson);
          this.basinsMap.fitBounds(this.basinGeoJson.getBounds());
          this.currentBasin = this.currentSubBasin = null;
        }
      }
    },

    /**
     **  Markers example
     **/
    // var marker = L.marker([27.7172, 85.3240]).addTo(map);
    // marker.bindPopup("This marker is pointing Kathmandu city.");
  },
};
</script>

<style>
.leaflet-container {
  background: #fff;
}
.back-btn {
  z-index: 800;
  position: absolute;
  left: 10px;
  top: 75px;
  width: 34px;
  height: 34px;
  background: #fff;
  border-radius: 5px;
  border: 2px solid rgba(0, 0, 0, 0.2);
  padding: 6px 9px;
  text-align: center;
  cursor: pointer;
}
.material-icons {
  font-size: 18px !important;
  font-weight: 600 !important;
}
</style>
