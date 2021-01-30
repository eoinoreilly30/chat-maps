<template>
  <div id="map" style="height: 88%"></div>
</template>

<script>
import Peer from "peerjs";

export default {
  name: "Home",
  data() {
    return {
      map: {},
      myPeer: {},
      myPosition: {},
      ids: [],
      positions: []
    };
  },
  beforeMount() {
    this.myPeer = new Peer("", {
      host: "chatmaps.art",
      port: 9000,
      secure: true
    });

    // TODO: handle error in background
    this.myPeer.on("open", id => {
      this.myPosition.id = id;
    });

    this.myPeer.on("connection", conn => {
      conn.on("data", data => {
        console.log("Received", data);
        this.positions[data.id] = { lat: data.lat, long: data.long };
        this.drawMarkers(this.positions);
      });

      conn.send(this.myPosition);
    });
  },
  mounted() {
    navigator.geolocation.getCurrentPosition(position => {
      this.myPosition.lat = position.coords.latitude;
      this.myPosition.long = position.coords.longitude;

      // eslint-disable
      // eslint-disable-next-line no-undef
      this.map = L.map("map").setView(
        [this.myPosition.lat, this.myPosition.long],
        13
      );

      //eslint-enable
      // eslint-disable-next-line
      L.tileLayer(
        "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiZW9pbm9yZWlsbHkzMCIsImEiOiJja2p5emVhbmcwMml1MnN0Zzdqd2p2cGU5In0.AciktlS6oKhpDWlJlyBDoA",
        {
          maxZoom: 18,
          id: "mapbox/streets-v11",
          tileSize: 512,
          zoomOffset: -1
        }
      ).addTo(this.map);

      // eslint-disable-next-line
      L.circleMarker([this.myPosition.lat, this.myPosition.long], {
        color: "black",
        fillColor: "#FCA156",
        weight: 1,
        fillOpacity: 1,
        radius: 10,
        id: "test"
      }).addTo(this.map);
    });

    setInterval(this.refresh, 5000);
  },
  methods: {
    refresh() {
      this.axios
        .get("https://chatmaps.art:9000/peerjs/peers")
        .then(response => {
          this.ids = response.data.filter(id => id !== this.myPosition.id);
          this.ids.map(id => this.getLocation(id));
        });
    },
    getLocation(id) {
      let conn = this.myPeer.connect(id);
      conn.on("open", () => {
        conn.send(this.myPosition);
      });
    },
    drawMarkers(positions) {
      Object.keys(positions).map(key => {
        // eslint-disable-next-line
        let circle = L
          .circleMarker([positions[key].lat, positions[key].long], {
          color: "black",
          fillColor: "#ff00ff",
          weight: 1,
          fillOpacity: 1,
          radius: 10,
          id: key
        }).addTo(this.map);

        circle.on("click", function() {
          console.log(this.options.id);
        });
      });
    }
  }
};
</script>
