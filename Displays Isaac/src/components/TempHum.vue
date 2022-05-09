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
    // Documentation found at http://www.eclipse.org/paho/clients/js/
    // or http://www.eclipse.org/paho/files/jsdoc/index.html

    // Create a client (host,port,unique client id)
    let client = new Paho.MQTT.Client(
      "mqtt.fhict.nl",
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
      userName: "i461941_test",
      password: "Shs6TiHWXfcjSd",
    });

    // called when the client connects
    function onConnect() {
      // Once a connection has been made, make a subscription and send a message.
      console.log("onConnect");

      //Subscribe to the topic we want to listen to (same as the one we are sending data too.)
      client.subscribe("public/i461941_isaac/#"); //+ that.sensor + "/#");
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
        that.temp = message.payloadString;
      }
      if(message.destinationName.endsWith("humidity")){
        that.hum = message.payloadString;
      }
    }
  },
};
</script>

<template>
  <h1>{{ temp }}</h1>
</template>

<style scoped>
</style>
