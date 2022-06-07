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
<div class="weather-wrapper">
    <div class="weather-card temperature">
        <div class="weather-icon sun"></div>
        <h1 v-if="!isNaN(temp)">{{temp}} &#8451; </h1>
        <h3 v-else> No data received</h3>
        <p>Temperature</p>
    </div>
    <div class="weather-card humidity">
        <h1 v-if="!isNaN(hum)">{{hum}} &#37; </h1>
        <h3 v-else> No data received</h3>
        <p>Humidity</p>
    </div>
</div>
</template>

<style scoped>
@import url(https://fonts.googleapis.com/css?family=Lato:100,300,400,700,900);

*, *:before, *:after {
  box-sizing: border-box;
}

.weather-wrapper {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
  text-align: center;
  min-height: 90vh;
}

.weather-card {
    
    margin:  20px 5px;
    border-radius: 20px;
    position: relative;
    overflow: hidden;
    width: 270px;
    height: 270px;
    background-color: #42b883;
    box-shadow: 0px 0px 25px 1px rgba(50, 50, 50, 0.1);
    animation: appear 500ms ease-out forwards;
}
    h1 {
        position: absolute;
        font-family: 'Lato', sans-serif;
        font-weight:300;
        font-size:60px;
        color: #35495e;
        bottom: 20px;
        left: 35px;
        opacity: 0;
        transform: translateX(150px);
        animation: title-appear 500ms ease-out 500ms forwards;
    }
    
    h3 {
        position: absolute;
        text-align: left;
        font-family: 'Lato', sans-serif;
        font-weight:300;
        font-size:40px;
        color: #35495e;
        bottom: 30px;
        left: 40px;
        opacity: 0;
        transform: translateX(150px);
        animation: title-appear 500ms ease-out 500ms forwards;
    }

    p {
        position: absolute;
        font-family: 'Lato', sans-serif;
        font-weight:300;
        font-size:28px;
        color: lighten(#35495e, 10%);
        bottom: 0;
        left: 35px;
        
    }

@keyframes appear {
  0% {
    transform:scale(0);
  }
  50% {
    transform:scale(1.05)
  }
  75% {
    transform:scale(0.95)
  }
  100% {
   transform:scale(1)
  }
}

@keyframes title-appear {
    from {
        opacity: 0;
        transform: translateX(0px);
    }
    to {
        opacity: 1;
        transform: translateX(0px);
    }
}
</style>
