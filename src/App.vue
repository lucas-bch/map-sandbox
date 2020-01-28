<template>
  <div class="map">
    <MglMap
      :accessToken="accessToken"
      :mapStyle.sync="mapStyle"
      :center="center"
      :zoom="zoom"
      :minZoom="3"
      :maxZoom="18"
      @load="onMapLoaded"
      ref="mapboxDiv"
    >
      <MglNavigationControl position="top-left" :showCompass="false" />
      <MglGeolocateControl position="top-left" />
      <MglScaleControl position="bottom-left" unit="metric" />
    </MglMap>
  </div>
</template>

<script>
import Mapbox from "mapbox-gl";
import {
  MglMap,
  MglNavigationControl,
  MglGeolocateControl,
  MglScaleControl
} from "vue-mapbox";

export default {
  components: {
    MglNavigationControl,
    MglGeolocateControl,
    MglScaleControl,
    MglMap
  },
  data() {
    return {
      accessToken: "noToken", // your access token. Needed if you using Mapbox maps
      mapStyle: {
        version: 8,
        sources: {
          "simple-tiles": {
            type: "raster",
            tiles: [
              "https://wxs.ign.fr/" +
              "choisirgeoportail" + // clé démo geoportail
                "/geoportail/wmts?" +
                "&REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0" +
                "&STYLE=normal" +
                "&TILEMATRIXSET=PM" +
                "&FORMAT=image/jpeg" +
                "&LAYER=ORTHOIMAGERY.ORTHOPHOTOS" +
                "&TILEMATRIX={z}" +
                "&TILEROW={y}" +
                "&TILECOL={x}"
            ],
            opacity: "1",
            attribution: "IGN-F/Géoportail",
            tileSize: 256
          },
          "other-tiles": {
            type: "raster",
            tiles: [
              "https://wxs.ign.fr/" +
              "choisirgeoportail" + // clé démo geoportail
                "/geoportail/wmts?" +
                "&REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0" +
                "&STYLE=bdparcellaire" +
                "&TILEMATRIXSET=PM" +
                "&FORMAT=image/png" +
                "&LAYER=CADASTRALPARCELS.PARCELS" +
                // "&LAYER= CADASTRALPARCELS.PARCELLAIRE_EXPRESS:parcelle" + // WFS not wmts
                "&TILEMATRIX={z}" +
                "&TILEROW={y}" +
                "&TILECOL={x}"
            ],
            opacity: "1",
            attribution: "IGN-F/Géoportail",
            tileSize: 256
          }
          // "geographical-names": {
          //   type: "raster",
          //   tiles: [
          //     "https://wxs.ign.fr/" +
          //     "choisirgeoportail" + // clé démo geoportail
          //       "/geoportail/wmts?" +
          //       "&REQUEST=GetTile&SERVICE=WMTS&VERSION=1.0.0" +
          //       "&STYLE=normal" +
          //       "&TILEMATRIXSET=PM" +
          //       "&FORMAT=image/png" +
          //       "&layer=GEOGRAPHICALNAMES.NAMES" +
          //       "&TILEMATRIX={z}" +
          //       "&TILEROW={y}" +
          //       "&TILECOL={x}"
          //   ],
          //   opacity: "1",
          //   attribution: "IGN-F/Géoportail",
          //   tileSize: 256
          // }
        },
        layers: [
          {
            id: "simple-tiles",
            type: "raster",
            source: "simple-tiles",
            minzoom: 0,
            maxzoom: 24
          },
          {
            id: "other-tiles",
            type: "raster",
            source: "other-tiles",
            minzoom: 0,
            maxzoom: 24
          }
          // {
          //   id: "geographical-names-tiles",
          //   type: "raster",
          //   source: "geographical-names",
          //   minzoom: 0,
          //   maxzoom: 24
          // }
        ]
      },
      zoom: 13,
      center: [5.150809, 44.688729]
    };
  },

  created() {
    // We need to set mapbox-gl library here in order to use it in template
    this.mapbox = Mapbox;
  },
  methods: {
    onMapLoaded(event) {
      this.map = event.map;
      this.map.on(
        "click",
        function(e) {
          // eslint-disable-next-line no-console
          console.log(e);
          new Mapbox.Popup()
            .setLngLat(e.lngLat)
            .setHTML(
              "<div class='popup-marker'> Hello !<br/>lng : " +
                e.lngLat.lng +
                "<br/> lat : " +
                e.lngLat.lat +
                " </div>"
            )
            .addTo(this.map);
        }.bind(this)
      );
      return;
    }
  }
};
</script>
<style lang="scss">
@import "~@/../node_modules/mapbox-gl/dist/mapbox-gl.css";
body {
  margin: 0;
}
.map {
  height: 100%;
  width: 100%;
  position: absolute;
}
.popup-marker {
  color: darkslategray;
  padding: 10px;
}
</style>