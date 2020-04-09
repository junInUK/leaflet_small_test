<template>
  <div style="height: 700px; width: 100%">
    <div style="height: 200px overflow: auto;">
      <p>First marker is placed at {{ withPopup.lat }}, {{ withPopup.lng }}</p>
      <p>Center is at {{ currentCenter }} and the zoom is: {{ currentZoom }}</p>
      <button @click="showLongText">
        Toggle long popup
      </button>
      <button @click="showMap = !showMap">
        Toggle map
      </button>
      <form id="sightings-form" v-on:submit="handleSubmit">
          <h2>Add an address</h2>
          <div class="formWrap">
              <label for="address">Please input your address</label>
              <input type="text" id="address" v-model="address"/>
          </div>
          <div class="formWrap">
              <label for="postcode">Please input your postcode</label>
              <input type="text" id="postcode" v-model="postcode"/>
          </div>
          <input type="submit" value="add" id="add"/>
      </form>
    </div>
    <l-map
      v-if="showMap"
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      style="height: 80%"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
      />
      <l-marker :lat-lng="withPopup">
        <l-popup>
          <div @click="innerClick">
            I am a popup
            <p v-show="showParagraph">
              Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque
              sed pretium nisl, ut sagittis sapien. Sed vel sollicitudin nisi.
              Donec finibus semper metus id malesuada.
            </p>
          </div>
        </l-popup>
      </l-marker>
      <l-marker :lat-lng="withTooltip">
        <l-tooltip :options="{ permanent: true, interactive: true }">
          <div @click="innerClick">
            I am a tooltip
            <p v-show="showParagraph">
              Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque
              sed pretium nisl, ut sagittis sapien. Sed vel sollicitudin nisi.
              Donec finibus semper metus id malesuada.
            </p>
          </div>
        </l-tooltip>
      </l-marker>
       <l-marker :lat-lng="[55.952288252747884, -3.193317260527555]">
        <l-icon
          :icon-size="dynamicSize"
          :icon-anchor="dynamicAnchor"
          icon-url="../assets/logo.png"
        />
      </l-marker>
    </l-map>
  </div>
</template>

<script>
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker, LPopup, LTooltip, LIcon } from "vue2-leaflet";

export default {
  name: "Example",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    LTooltip,
    LIcon
  },
  data() {
    return {
      zoom: 13,
      center: latLng(55.9469563, -3.2019937),
    //   center: "498 Union Street, Aberdeen, UK",
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      withPopup: latLng(55.948750932784435, -3.200247152914807),
      withTooltip: latLng(55.9548018017003, -3.1841479250360756),
      currentZoom: 11.5,
    //   currentCenter: latLng(47.41322, -1.219482),
      currentCenter: "37, Castle Terrace, Lauriston, Tollcross, Edinburgh, City of Edinburgh, Scotland, EH1 2EL, United Kingdom",
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5
      },
      showMap: true,
      address: null,
      postcode: null,
      iconSize: 64
    };
  },
  computed: {
    dynamicSize() {
      return [this.iconSize, this.iconSize * 1.15];
    },
    dynamicAnchor() {
      return [this.iconSize / 2, this.iconSize * 1.15];
    }
  },
  methods: {
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    showLongText() {
      this.showParagraph = !this.showParagraph;
    },
    innerClick() {
      alert("Click!");
    },
    handleSubmit(e){
        e.preventDefault();
        // let address_postcode = this.address + this.postcode;
        
        let url = 'https://nominatim.openstreetmap.org/search?format=json&q=' + this.address;
        console.log(url);
        fetch(url)
         .then(response => response.json())
         .then(json_obj => console.log(json_obj))
        // .then(teas => this.teas = teas);
      //  .then(response => console.log(respon))
    }
  }
};
</script>

<style scoped>
h2 {
	margin: 10px 0;
	padding: 0;
}

form {
	width: 75%;
	margin: 0 auto;
	background: rgba(255, 255, 255, 0.7);
	padding: 20px;
	margin-bottom: 40px;
}

label {
	min-width: 100px;
	display: inline-block;
}

.formWrap {
	margin-bottom: 10px;
}
</style>

// $.get(location.protocol + '//nominatim.openstreetmap.org/search?format=json&q='+address, function(data){
//        console.log(data);
//     });