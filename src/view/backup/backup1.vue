<template>
    <div>
        <Tabs type="card" value="name1" @on-click="pickupTab">
            <TabPane label="Train" icon="ios-list-box" name="original"></TabPane>
            <TabPane label="Code" icon="md-cloud-upload" name="processed"></TabPane>
        </Tabs>
        <div v-if="oriDropdown">
          <Card shadow>
            <p slot="title" class="card-title" >
              <Icon type="md-desktop" size:="20"/>
              Camera
            </p>
                <div class='cam-form'>
                    <code v-if="device">{{ device.label }}</code>
                </div>
                <div>
                    <vue-web-cam
                        ref="webcam"
                        :device-id="deviceId"
                        width="100%"
                        @started="onStarted"
                        @stopped="onStopped"
                        @error="onError"
                        @cameras="onCameras"
                        @camera-change="onCameraChange"
                    />
                </div>

                <div class='cam-form' style="margin-bottom: 10px; margin-top: 10px;">
                    <select v-model="camera">
                        <option>-- Select Device --</option>
                        <option
                            v-for="device in devices"
                            :key="device.deviceId"
                            :value="device.deviceId"
                        >{{ device.label }}</option>
                    </select>
                </div>
                <div class='cam-form'> 
                    <button type="button" class="btn btn-primary" @click="onCapture">Capture Photo</button>
                    <button type="button" class="btn btn-danger" @click="onStop">Stop Camera</button>
                    <button type="button" class="btn btn-success" @click="onStart">Start Camera</button>
                </div>
          </Card>
        </div>
        <div v-if="proDropdown">
            <Card shadow>
                <p slot="title" class="card-title" >
                <Icon type="md-desktop" size:="20"/>
                Output Video
                </p>
                <div>
                    <img v-if="proDropdown" src="http://localhost:5000/motionDetection1"/>
                </div>
            </Card>
        </div>
    </div>


</template>

<script>
import { WebCam } from "vue-web-cam";
export default {
    name: "App",
    components: {
        "vue-web-cam": WebCam
    },
    data() {
        return {
            img: null,
            camera: null,
            deviceId: null,
            devices: [],
            oriDropdown: true,
            proDropdown: false, 
        };
    },
    computed: {
        device: function() {
            return this.devices.find(n => n.deviceId === this.deviceId);
        }
    },
    watch: {
        camera: function(id) {
            this.deviceId = id;
        },
        devices: function() {
            // Once we have a list select the first one
            const [first, ...tail] = this.devices;
            if (first) {
                this.camera = first.deviceId;
                this.deviceId = first.deviceId;
            }
        }
    },
    methods: {
        pickupTab (val) {
        if (val == "original"){
            console.log("pick up original tab")
            this.oriDropdown = true
            this.proDropdown = false
        }
        else {
            console.log("pick up processed tab")
            this.proDropdown = true
            this.oriDropdown = false
        }
        },
        onCapture() {
            this.img = this.$refs.webcam.capture();
        },
        onStarted(stream) {
            console.log("On Started Event", stream);
        },
        onStopped(stream) {
            console.log("On Stopped Event", stream);
        },
        onStop() {
            this.$refs.webcam.stop();
        },
        onStart() {
            this.$refs.webcam.start();
        },
        onError(error) {
            console.log("On Error Event", error);
        },
        onCameras(cameras) {
            this.devices = cameras;
            console.log("On Cameras Event", cameras);
        },
        onCameraChange(deviceId) {
            this.deviceId = deviceId;
            this.camera = deviceId;
            console.log("On Camera Change Event", deviceId);
        }
    }
};
</script>

<style scoped>
.cam-form {
  display: flex;
  justify-content: center;
}
</style>