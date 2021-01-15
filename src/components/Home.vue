<template>
  <div id="map" style="height: 88%"></div>
</template>

<script>
import Peer from "peerjs";

export default {
  name: "Home",
  data() {
    return {
      myId: "",
      ids: [],
      peer: {}
    };
  },
  beforeMount() {
    this.peer = new Peer("", {
      host: "chatmaps.art",
      port: 9000,
      secure: true
    });
    this.peer.on("open", id => {
      this.myId = id;
    });
  },
  mounted() {
    navigator.geolocation.getCurrentPosition(position => {
      // eslint-disable-next-line
      let leaflet = L
      let lat = position.coords.latitude;
      let long = position.coords.longitude;

      let map = leaflet.map("map").setView([lat, long], 13);

      leaflet
        .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiZW9pbm9yZWlsbHkzMCIsImEiOiJja2p5emVhbmcwMml1MnN0Zzdqd2p2cGU5In0.AciktlS6oKhpDWlJlyBDoA",
          {
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1
          }
        )
        .addTo(map);

      let circle = leaflet
        .circleMarker([lat, long], {
          color: "black",
          fillColor: "#FCA156",
          weight: 1,
          fillOpacity: 1,
          radius: 10,
          id: "test"
        })
        .addTo(map);

      circle.on("click", function() {
        // console.log(this.options.id);
      });
    });

    setInterval(this.refresh, 5000);
  },
  methods: {
    refresh() {
      this.axios
        .get("https://chatmaps.art:9000/peerjs/peers")
        .then(response => {
          this.ids = response.data.filter(id => id !== this.myId);
          console.log(this.myId, this.ids);
          this.ids.map(id => this.getLocation(id));
        });
    },
    getLocation(id) {
      console.log(id, this.myId);

      this.peer.on("connection", conn => {
        console.log("connection");

        conn.on("data", data => {
          console.log("data");
          console.log("Received", data);
        });
        // Send messages
        conn.send("Hello! its me" + this.myId);
      });

      let conn = this.peer.connect(id);
      conn.on("open", () => {
        console.log("opened");
        // Receive messages

        conn.on("data", data => {
          console.log("data");
          console.log("Received", data);
        });

        // Send messages
        conn.send("Hello! its me" + this.myId);
      });
    }
  }
};
</script>

<style scoped>
#map {
  height: 100%;
}
</style>
