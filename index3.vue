Skip to content Search or jump to… Pull requests Issues Marketplace Explore
@vishal-shinde-svg satya-auti / map_event_store_nuxt Public Code Issues Pull
requests Actions Projects Security Insights
map_event_store_nuxt/pages/database.vue @satya-auti satya-auti feat: stored Data
in DB Latest commit 5a82361 3 days ago History 1 contributor 525 lines (480
sloc) 14.2 KB

<template>
  <!--  class="w-screen h-screen" -->
  <!-- <main> -->
  <head>
    <meta charset="utf-8" />
    <title>Mapbox Event : Directions</title>
    <meta name="robots" content="noindex, nofollow" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <!-- Full Screen View -->
  <div class="main">
    <!-- Toggle Bar code -->
    <div class="left1 bg-gray">
      <div class="float-right">
        <button class="rounded-xl p-2 bg-black text-white">Refresh</button>
      </div>
      <br />
      <div class="">
        <h1 class="font-bold ml-2">Stored Events</h1>
        <br />
        <table class="">
          <tr>
            <th>Name</th>
            <th>Desc</th>
            <th>Toggle</th>
            <th>Opacity</th>
            <th>Delete</th>
          </tr>

          <tr v-for="(val, index) in data.mapEvent" :key="val.name">
            <td>{{ index }} ) {{ val.name }}</td>
            <td>{{ val.desc }}</td>
            <td>
              <!--   v-model="info.checkedInfo"
                          :value="data.coordinates" -->
              <!-- id="{{index}}"  v-bind:value="data.coordinates"  v-model="info.checkedInfo" -->
              <input
                type="checkbox"
                name="dataEvent"
                id="checkedData"
                @click="showDataOnMap($event, index)"
              />
            </td>
            <td>
              <!-- v-model="info.opacity" -->
              <input
                type="range"
                @change="changeColorOpacity(index)"
                min="1"
                max="10"
                v-model="info.opacity"
              />
            </td>
            <td>
              <a @click="deleteLayer(index)"
                ><img
                  class="rounded"
                  src="../assets/img/trash_2_2.png"
                  alt="delete"
              /></a>
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
                  <label for="desc">Description</label>
                </td>
                <td>
                  <input
                    type="text"
                    id="desc"
                    v-model="info.desc"
                    placeholder="Description"
                  />
                </td>
              </tr>
              <tr class="p-1">
                <td class="p-1">
                  <label for="color">Select Color</label>
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
                <!-- <td>
                  <button
                    @click="Cancel"
                    class="rounded-xl p-1 text-white bg-blue-500 hover:bg-red-600"
                  >
                    Cancel
                  </button>
                </td> -->
              </tr>
            </table>
          </div>
        </div>
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
  draw: {},
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
  data1: {
    name: "",
    desc: "",
    color: "",
    geom: {},
  },
  opacity: 1,
  show: false,
  name: "",
  desc: "",
  color: "",
  geom: {},
  changer: false,
  deleteIndex: null,
  mapEventData: [],
});
let db = reactive({
  dbObject: {
    name: "",
    desc: "",
    //properties: "",
    geom: {
      //   type: value.type,
      //   crs: value.crs,
      //   coordinates: [],
    },
  },
});
async function getMapData(tempMap: mapboxgl.Map) {
  data.map = tempMap;
  mapboxgl.accessToken =
    "pk.eyJ1Ijoic2F0eWEtYXV0aSIsImEiOiJjbDdwdnFqMWIwMWF3M3BxZ3dvaTZlNW5yIn0.wrAe-_808WZm-CBKVTwfIw";
  info.mapEventData = await $fetch("http://localhost:3050/map");
  console.log("all database data", info.mapEventData);
  if (info.changer == true) {
    let i = info.deleteIndex;
    // data.map.removeLayer(data.mapEvent[i].geom.id);
    // data.map.removeSource(data.mapEvent[i].geom.id);
    info.changer = false;
  }
  info.changer = false;
  //   data.map.addControl(new mapboxgl.NavigationControl());
  var Draw = new MapboxDraw({
    displayControlsDefault: true,
    // Select which mapbox-gl-draw control buttons to add to the map.
    controls: {
      polygon: true,
      //   trash: true,
    },
    // Set mapbox-gl-draw to draw by default.
    // The user does not have to click the polygon control button first.
    defaultMode: "draw_polygon",
  });
  data.draw = Draw;
  data.map.addControl(Draw, "top-right");
  data.map.on("draw.create", updateData);
  async function updateData(e) {
    info.show = true;
    console.log(e);
    console.log(e.features[0].id);
    console.log(e.features[0].geometry);
    // info.geom = e.features[0];
    info.geom = e.features[0].geometry;
  }
  //   info.changer = false;
}
// function getData(){
//     // http://localhost:3050/map
// }
async function submit() {
  // info.dataObject.name = name;
  //         db.dbObject.name = name;
  //         info.dataObject.desc = desc;
  //         db.dbObject.desc = desc;
  //         info.dataObject.coordinates = drawnFeature.geometry.coordinates;
  //         info.allData.push(info.dataObject);
  //         db.dbObject.geom.coordinates = info.dataObject.coordinates;
  //         console.log("co Data", info.dataObject.coordinates);
  db.dbObject = {
    name: info.name,
    desc: info.desc,
    //  properties: info.color,
    geom: info.geom,
  };
  let response = await $fetch("http://localhost:3001/mapg", {
    method: "POST",
    // body: payload,
    body: db.dbObject,
  });
  console.log("res", response);
  db.dbObject = {
    name: "",
    desc: "",
    //properties: "",
    geom: {},
  };
}
// // // this function is used to show and hide mapEvent
// function showDataOnMap(e, index) {
//   console.log(e.target.checked);
//   //   let id = `${data.mapEvent[index].id}`;
//   console.log("data.layers ", data.mapEvent);
//   if (e.target.checked == true) {
//     info.changer = false;
//     var x;
//     let colorPick;
//     let flyToLocation;
//     let flyToLocation1;
//     let colorSelect = data.mapEvent.filter((e) => {
//       if (e.geom.id == data.mapEvent[index].geom.id) {
//         // flyToLocation = data.mapEvent[index].geom.geometry.coordinates[index];
//         flyToLocation = e.geom.geometry.coordinates[0];
//         flyToLocation1 = e.geom.geometry.coordinates;
//         colorPick = e.color;
//         return e.color;
//       }
//     });
//     // flyToLocation = data.mapEvent[index].geom.geometry.coordinates[index];
//     console.log("flyloc", flyToLocation);
//     console.log("typeData", data.mapEvent[index].geom);
//     let type = data.mapEvent[index].geom.geometry.type;
//     let latlngArr = [];
//     if (type == "LineString") {
//       console.log("It is LineString");
//       flyToLocation.map((e) => {
//         console.log("e", e);
//         latlngArr.push(e);
//       });
//     }
//     if (type == "Polygon") {
//       console.log("It is Polygon");
//       latlngArr = flyToLocation[0];
//     }
//     if (type == "Point") {
//       console.log("It is Point");
//       console.log("point data", flyToLocation1);
//       console.log("point data1", flyToLocation1[0]);
//       console.log("point data2", flyToLocation1[1]);
//       latlngArr = [flyToLocation1[0], flyToLocation1[1]];
//       //   var x = data.map.marker([latlngArr[0], latlngArr[1]]).addTo(data.map);
//       x = new mapboxgl.Marker({
//         draggable: true,
//         color: colorPick,
//         // color: "#" + (Math.random().toString(16) + "000000").substring(2, 8),
//       })
//         .setLngLat([latlngArr[0], latlngArr[1]])
//         //   .setLngLat([74.04931277036667, 19.266912177018096])
//         .addTo(data.map);
//     }
//     let latlng = flyToLocation[0];
//     console.log("latlng", latlng);
//     // console.log("exact value", latlng[0], latlng[1]);
//     console.log("exact value latlng array ", latlngArr);
//     // Fly to a random location
//     data.map.flyTo({
//       //   center: [(Math.random() - 0.5) * 360, (Math.random() - 0.5) * 100],
//       //   center: [latlng[0], latlng[1]],
//       center: [latlngArr[0], latlngArr[1]],
//       zoom: 6,
//       essential: true, // this animation is considered essential with respect to prefers-reduced-motion
//     });
//     // console.log("selected flyToLocation", flyToLocation[index]);
//     console.log("selected color", colorSelect);
//     console.log("Pick color", colorPick);
//     console.log("checked", data.mapEvent[index].geom.id);
//     const geometry: any = data.mapEvent[index].geom;
//     console.log("geometry", geometry);
//     // data.mapEvent.push(data1);
//     let sId: string = data.mapEvent[index].geom.id;
//     let randonNo = Math.round(Math.random() * 1e9);
//     console.log("randonNo", randonNo);
//     // data.map.addSource(sId, {
//     //   type: "geojson",
//     //   data: geometry,
//     // });
//     data.map.addSource(data.mapEvent[index].geom.id, {
//       type: "geojson",
//       data: geometry,
//     });
//     data.map.addLayer({
//       //   id: randonNo,
//       id: data.mapEvent[index].geom.id,
//       //   source: sId,
//       //   source: data.mapEvent[0].geom.id,
//       source: data.mapEvent[index].geom.id,
//       type: "fill",
//       layout: {},
//       paint: { "fill-color": colorPick, "fill-opacity": 0.5 },
//     });
//   } else {
//     data.map.removeLayer(data.mapEvent[index].geom.id);
//     data.map.removeSource(data.mapEvent[index].geom.id);
//     // var marker = new mapboxgl.Marker().addTo(data.map);
//     // x.remove();
//     // data.map.remove(x);
//     data.map.marker.remove(x);
//   }
//   // console.log(data.mapEvent[index].geom.coordinates);
// }
// function submit() {
//   info.data1 = {
//     name: info.name,
//     desc: info.desc,
//     color: info.color,
//     geom: info.geom,
//   };
//   console.log("data entered ", info.data1);
//   data.mapEvent.push(info.data1);
//   info.name = "";
//   info.color = "";
//   info.desc = "";
//   info.geom = {};
//   info.show = false;
//   data.draw.deleteAll();
//   info.changer = false;
//   //   data.map.removeLayer(data.polygon);
// }
// function changeColorOpacity(index) {
//   console.log("opacity", index, info.opacity);
//   let colorOpacity = info.opacity;
//   let colorPick;
//   // let flyToLocation;
//   let colorSelect = data.mapEvent.filter((e) => {
//     if (e.geom.id == data.mapEvent[index].geom.id) {
//       // flyToLocation = data.mapEvent[index].geom.geometry.coordinates[index];
//       // flyToLocation = e.geom.geometry.coordinates[0];
//       colorPick = e.color;
//       return e.color;
//     }
//   });
//   data.map.removeLayer(data.mapEvent[index].geom.id);
//   data.map.removeSource(data.mapEvent[index].geom.id);
//   data.map.addSource(data.mapEvent[index].geom.id, {
//     type: "geojson",
//     data: data.mapEvent[index].geom,
//   });
//   data.map.addLayer({
//     //   id: randonNo,
//     id: data.mapEvent[index].geom.id,
//     //   source: sId,
//     //   source: data.mapEvent[0].geom.id,
//     source: data.mapEvent[index].geom.id,
//     type: "fill",
//     layout: {},
//     paint: { "fill-color": colorPick, "fill-opacity": colorOpacity / 10 },
//   });
// }
// function deleteLayer(i) {
//   console.log("delete", i);
//   var confirmation = confirm(" Click OK to delete data !!!");
//   if (confirmation == true) {
//     let index = i + 1;
//     data.mapEvent.splice(index, 1);
//     info.changer = true;
//     info.deleteIndex = i;
//     data.map.removeLayer(data.mapEvent[i].geom.id);
//     data.map.removeSource(data.mapEvent[i].geom.id);
//     getMapData(data.map);
//     // data.map.removeLayer(data.mapEvent[i].geom.id);
//     // data.map.removeSource(data.mapEvent[i].geom.id);
//   } else {
//     alert("Something goes wrong!!!");
//   }
//   //   data.mapEvent.splice(i, 1);
// }
// function Cancel() {
//   info.show = false;
// }
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
  width: 30vw;
  float: left;
}
.right1 {
  height: 100vh;
  width: 70vw;
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
</style>
