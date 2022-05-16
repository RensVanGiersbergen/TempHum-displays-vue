<script>
export default {
  props: ["sensor"],
  data() {
    return {
      temp: NaN,
      hum: NaN
    };
  },
  mounted() {
    let that = this;

    // Create a client (host,port,unique client id)
    let client = new Paho.MQTT.Client(
      "0b98e3d1a1514d3688e16cf487c754ae.s2.eu.hivemq.cloud",
      8884,
      (Math.random() * 1e32).toString(36)
    );
    // set callback handlers
    client.onConnectionLost = onConnectionLost;
    client.onMessageArrived = onMessageArrived;

    // connect the client
    client.connect({
      onSuccess: onConnect,
      onFailure: onFailure,
      useSSL: true,
      userName: "Thims",
      password: import.meta.env.VITE_PASSWORD_BROKER
    });

    // called when the client connects
    function onConnect() {
      // Once a connection has been made, make a subscription and send a message.
      console.log("Succesfully connected with broker");

      //Subscribe to the topic we want to listen to (same as the one we are sending data too.)
      client.subscribe(that.sensor + "/#");
    }

    function onFailure(responseObject) {
      console.log("onFailure: ", responseObject);
    }

    // called when the client loses its connection
    function onConnectionLost(responseObject) {
      if (responseObject.errorCode !== 0) {
        console.log("onConnectionLost:" + responseObject.errorMessage);
      }
    }

    // called when a message arrives
    function onMessageArrived(message) {
      console.log(message);
      if(message.destinationName.endsWith("temperature")){
        that.temp = message.payloadString + " °C";
      }
      if(message.destinationName.endsWith("humidity")){
        that.hum = message.payloadString;
      }
    }
  },
};
</script>

<template>
 <transition name="slide-fade" mode="out-in">
  <div class="lol" :key="temp">
    <h1>
      {{temp}}
    </h1>
  </div>
 </transition>
</template>

<style scoped>
.lol{
  color:#42b883;
  position:absolute;
  top: 40%;
  left: 50%;
  -ms-transform: translateY(-50%);
  transform: translateY(-50%);
  -ms-transform: translateX(-50%);
  transform: translateX(-50%);
}
.slide-fade-enter-active {
  transition: all .8s ease-in;
}
.slide-fade-leave-active {
  transition: all .8s ease-out;
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active for <2.1.8 */ {
  opacity: 0;
}
</style>
