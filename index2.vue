<template>
  <head>
    <meta charset="utf-8" />
    <title>Mapbox</title>
    <meta name="robots" content="noindex, nofollow" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <!-- Full Screen View -->
  <div class="main">
    <!-- Toggle Bar code -->
    <div class="left1 bg-gray]">
      <br />
      <div class="">
        <h1 class="font-bold ml-2">layer</h1>

        <div class="map-overlay top">
          <div class="map-overlay-inner">
            <label>Layer opacity: <span id="slider-value">100%</span></label>
            <input
              id="slider"
              type="range"
              min="0"
              max="100"
              step="0"
              value="100"
            />
          </div>
        </div>
        <br />
        <table class="">
          <tr>
            <th>Name</th>
            <th>tag</th>
            <th>Add/remove</th>
          </tr>
          <tr v-for="(item, index) in data.mapEvent" :key="item">
            <td>{{ index }} ) {{ item.name }}</td>
            <td>{{ item.tag }}</td>
            <td>
              <input
                type="checkbox"
                name="dataEvent"
                id="checkedData"
                @click="showDataOnMap($event, index)"
              />
            </td>
          </tr>
        </table>
      </div>
    </div>
    <!-- Map  -->
    <div class="right1">
      <v-map class="w-full h-full" :options="state.map" @loaded="getMapData">
        <div class="zIndex1 bg-slate-500 rounded-lg w-5/12" v-show="info.show">
          <div class="p-2">
            <table class="p-2">
              <tr class="p-1">
                <td class="p-1">
                  <label for="name">Name</label>
                </td>
                <td>
                  <input
                    type="text"
                    id="name"
                    v-model="info.name"
                    placeholder="Name"
                  />
                </td>
              </tr>
              <tr class="p-1">
                <td class="p-1">
                  <label for="desc">tag</label>
                </td>
                <td>
                  <input
                    type="text"
                    id="desc"
                    v-model="info.tag"
                    placeholder="Description"
                  />
                </td>
              </tr>
              <tr class="p-1">
                <td class="p-1">
                  <label for="color">Color</label>
                </td>
                <td>
                  <input type="color" id="color" v-model="info.color" />
                </td>
              </tr>
              <tr class="p-1">
                <td class="p-1">
                  <button
                    @click="submit"
                    class="rounded-xl p-1 text-white bg-blue-500 hover:bg-blue-600"
                  >
                    Submit
                  </button>
                </td>
                <td>
                  <button
                    @click="Cancel"
                    class="rounded-xl p-1 text-white bg-blue-500 hover:bg-red-600"
                  >
                    Cancel
                  </button>
                </td>
              </tr>
            </table>
          </div>
        </div>
        -->
      </v-map>
    </div>
  </div>
  <!-- </main> -->
</template>
<script setup lang="ts">
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import MapboxDraw from "@mapbox/mapbox-gl-draw";

const data: any = reactive({
  map: {} as mapboxgl.Map,
  mapEvent: [],
});
const state = reactive({
  map: {
    accessToken:
      "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    // center: [444.04931277036667, 26.266912177018096] as number[], //uses longitude, latitude
    center: [74.04931277036667, 18.266912177018096] as number[],
    zoom: 7,
    maxZoom: 22,
    crossSourceCollisions: false,
    failIfMajorPerformanceCaveat: false,
    attributionControl: false,
    preserveDrawingBuffer: true,
    // container: "map",
  } as mapboxgl.MapboxOptions,
});

let info = reactive({
  // allData: [],
  // checkedInfo: [],
  // dataObject: {
  //   name: "",
  //   tag: "",
  //   coordinates: [],
  //},
  // pointsData: [],
  data1: {
    name: "",
    tag: "",
    color: "",
    geom: {},
  },
  //arr: [],
  show: false,
  name: "",
  tag: "",
  color: "",
  geom: {},
});
async function getMapData(tempMap: mapboxgl.Map) {
  data.map = tempMap;
  mapboxgl.accessToken =
    "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw";
  // map.addControl(
  //   new MapboxGeocoder({
  //     accessToken: mapboxgl.accessToken,
  //     mapboxgl: mapboxgl,
  //   })
  // );
  //   data.map.addControl(new mapboxgl.NavigationControl());
  var Draw = new MapboxDraw();
  data.map.addControl(Draw, "top-left");
  data.map.on("draw.create", updateData);
  // data.map.on("draw.delete", updateArea);
  // data.map.on("draw.update", updateArea);

  async function updateData(e) {
    info.show = true;
    console.log(e);
    console.log(e.features[0].id);
    console.log(e.features[0].geometry);
    // create object
    // let name = prompt("Enter the Name ");
    // let desc = prompt("Enter the Desc ");
    // let color = prompt("<Input type='color'>");
    // let coordinates = e.features[0].geometry.coordinates;
    // let geometry = e.features[0].geometry;
    // let type1 = e.features[0].geometry.type;
    // let data1 = {
    //   id: `${e.features[0].id}`,
    //   name: `${name}`,
    //   type: `${type1}`,
    //   coordinates: `${coordinates}`,
    //   geometry: `${geometry}`,
    // };
    // let data1: any = e.features[0];
    info.geom = e.features[0];
    // info.data1 = {
    //   name: info.name,
    //   desc: info.desc,
    //   color: info.color,
    //   geom: e.features[0],
    // };
    // console.log("data entered ", info.data1);
    // const geometry: any = data.mapEvent[index].geom;
    // let data1 = {
    //   geom: {};
    // };
    // data.map.addSource("pune", {
    //   type: "geojson",
    //   data: data.mapEvent[0],
    //   // {
    //   //   type: "Feature",
    //   //   geometry,
    //   // } as any,
    // });
    // console.log(coordinates);
    // console.log(type1);
  }
}
// // this function is used to show and hide mapEvent
function showDataOnMap(e, index) {
  console.log(e.target.checked);
  //   let id = `${data.mapEvent[index].id}`;
  console.log("data.layers ", data.mapEvent);
  if (e.target.checked == true) {
    // data.map.addSource(data.mapEvent[0].id, {
    //   type: "geojson",
    //   data: data.mapEvent[0],
    // });
    let colorPick;
    let colorSelect = data.mapEvent.filter((e) => {
      if (e.geom.id == data.mapEvent[index].geom.id) {
        colorPick = e.color;
        return e.color;
      }
    });
    console.log("selected color", colorSelect);
    console.log("Pick color", colorPick);
    console.log("checked", data.mapEvent[index].geom.id);
    const geometry: any = data.mapEvent[index].geom;
    console.log("geometry", geometry);
    // data.mapEvent.push(data1);
    let sId: string = data.mapEvent[index].geom.id;
    let randonNo = Math.round(Math.random() * 1e9);
    console.log("randonNo", randonNo);
    // data.map.addSource(sId, {
    //   type: "geojson",
    //   data: geometry,

    const slider = document.getElementById("slider");
    const sliderValue = document.getElementById("slider-value");

    data.map.addSource(data.mapEvent[index].geom.id, {
      type: "geojson",
      data: geometry,
    });
    data.map.addLayer({
      //   id: randonNo,
      id: data.mapEvent[index].geom.id,
      //   source: sId,
      //   source: data.mapEvent[0].geom.id,
      source: data.mapEvent[index].geom.id,
      type: "fill",
      layout: {},
      paint: { "fill-color": colorPick, "fill-opacity": 0.5 },
    });
  } else {
    // data.map.removeSource(data.mapEvent[0].id);
    data.map.removeLayer(data.mapEvent[index].geom.id);
    data.map.removeSource(data.mapEvent[index].geom.id);
  }
  // console.log(data.mapEvent[index].geom.coordinates);
}
function submit() {
  info.data1 = {
    name: info.name,
    tag: info.tag,
    color: info.color,
    geom: info.geom,
  };
  console.log("data entered ", info.data1);
  data.mapEvent.push(info.data1);
  info.name = "";
  info.color = "";
  info.tag = "";
  info.geom = {};
  //data.draw.deleteAll();
  info.show = false;
}
function Cancel() {
  info.show = false;
}
</script>
<style>
html,
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.w-screen {
  width: 100vw;
}
.main {
  height: 100vh;
  width: 100vw;
}
.left1 {
  height: 100vh;
  width: 25vw;
  float: left;
}
.right1 {
  height: 100vh;
  width: 75vw;
  float: right;
  position: relative;
}
.h-screen {
  height: 100vh;
}
.h-full {
  height: 100%;
}
.w-full {
  width: 100%;
}
.bg-gray {
  background-color: cadetblue;
}
.zIndex {
  position: absolute;
  left: 0px;
  top: 0px;
  z-index: 1;
}
.zIndex1 {
  position: relative;
  left: 300px;
  top: 100px;
  z-index: 1;
}
/* .zIndex2 {
          position: relative;
          left: 400px;
          top: 0px;
          z-index: 1;
        } */

.map-overlay {
  font: bold 12px/20px "Helvetica Neue", Arial, Helvetica, sans-serif;
  position: left;
  width: 75%;
  top: 0;
  left: 0;
  padding: 10px;
}

.map-overlay .map-overlay-inner {
  background-color: #fff;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
  border-radius: 3px;
  padding: 10px;
  margin-bottom: 10px;
}

.map-overlay label {
  display: block;
  margin: 0 0 10px;
}

.map-overlay input {
  background-color: transparent;
  display: inline-block;
  width: 100%;
  position: relative;
  margin: 0;
  cursor: ew-resize;
}
</style>
