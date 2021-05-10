<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Ionic Vue - Push Notification</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Ionic Vue - Push Notification</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
        <strong>FCM Token</strong><br>
        <ion-button @click="showToken()">Show token</ion-button>
         <p class="selectext" v-if="viewToken">{{token}}</p>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, getPlatforms } from '@ionic/vue';
import { defineComponent } from 'vue';
import { FCM } from '@capacitor-community/fcm';
import { Plugins, Capacitor } from '@capacitor/core';
const { PushNotifications } = Plugins;

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar
  },
  data() {
    return {
    fcm: new FCM(),
    token: '',
    viewToken: false
    }
  },
  mounted() {

    // Check the device with getPlatorms
    console.log("Ionic platform is: " + getPlatforms());

    // Check the device with Capacitor
    const device = Capacitor.getPlatform();
    console.log("Capacitor platform is: " + device);

    if(device == "ios") {
      // Request permission to use push notifications
    // iOS will prompt user and return if they granted permission or not
    // Android and web will just grant without prompting
     PushNotifications.requestPermission().then(result => {
      alert("result " + JSON.stringify(result));
    });
    }
         // Add registration error if there are.
        PushNotifications.addListener("registrationError", (error) => {
          console.log(`error on register ${JSON.stringify(error)}`);
        }),
        // Add Notification received
          PushNotifications.addListener(
            "pushNotificationReceived",
            (notification) => {
              console.log(`notification ${JSON.stringify(notification)}`);
            }
          ),
          // Add Action performed
          PushNotifications.addListener(
            "pushNotificationActionPerformed",
            async (notification) => {
                alert("notification " + notification)
              console.log("notification succeeded");
            }
          ),
          // Initialize the registration with FMC Token
          PushNotifications.register();
        const fcmToken = this.fcm.getToken();
        this.token = JSON.stringify(fcmToken);
        console.log("token:" + JSON.stringify(fcmToken));
        
  },
  methods: {
    async showToken() {
      this.token = await this.fcm.getToken().then(function(result: any) {
        console.log(result);
        return result
      }, function(err) {
          console.log(err);
         return err
      });
        this.viewToken = true;
    },
  },
});
</script>

<style scoped>
#container {
  text-align: center;
  
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}

.selectext {
  -webkit-user-select: auto;
}
</style>