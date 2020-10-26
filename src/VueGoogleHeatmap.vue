<template>
  <div ref="map" :style="`width: ${mapWidth}; height: ${mapHeight}`"></div>
</template>

<script>
import { loaded } from "./loader";

export default {
  name: "vue-google-heatmap",
  props: {
    lat: {
      type: Number,
      default: () => 37.775,
    },
    lng: {
      type: Number,
      default: () => -122.434,
    },
    initialZoom: {
      type: Number,
      default: () => 13,
    },
    mapType: {
      type: String,
      default: () => "roadmap",
    },
    points: {
      type: Array,
      default: () => [],
    },
    city_points: {
      type: Array,
      default: () => [],
    },
    width: {
      type: [String, Number],
      default: () => "100%",
    },
    height: {
      type: [String, Number],
      default: () => "100%",
    },
    radius: {
      type: Number,
      default: () => 5,
    },
    opacity: {
      type: Number,
      default: () => 0.8,
    },
    dissipating: {
      type: Boolean,
      default: () => false,
    },
    gradient: {
      type: Array,
      required: false,
    }
  },
  computed: {
    mapWidth() {
      if (typeof this.width === "string") {
        return this.width;
      } else {
        return `${this.width}px`;
      }
    },
    mapHeight() {
      if (typeof this.height === "string") {
        return this.height;
      } else {
        return `${this.height}px`;
      }
    },
    heatmapPoints() {
      return this.points.map(
        (point) => new google.maps.LatLng(point.lat, point.lng)
      );
    },
    cityPoints() {
      return this.city_points.map(
        (point) => new google.maps.LatLng(point.lat, point.lng)
      );
    }
  },
  created() {
    return loaded.then(() => {
      const mapElement = this.$refs.map;
      
      let mapObj = this.$mapObject = new google.maps.Map(mapElement, {
        zoom: this.initialZoom,
        center: { lat: this.lat, lng: this.lng },
        mapTypeId: this.mapType,
      });

      this.cityPoints.forEach(point => {
        let myMarkerOptions = {
          position: point,
          map: mapObj
        }

        let myMarker = new google.maps.Marker(myMarkerOptions);
      })

      this.$heatmap = new google.maps.visualization.HeatmapLayer({
        data: this.heatmapPoints,
        map: this.$mapObject,
        radius: this.radius,
        opacity: this.opacity,
        dissipating: this.dissipating,
        gradient: this.gradient
      });

      this.$heatmap.setMap(this.$mapObject);
    });
  },
};
</script>
