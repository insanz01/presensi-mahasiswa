<template>
  <div class="home">
  	<v-container>
  		<v-row>
  			<v-col cols=12>
  				<h1 @click="decodedString = ''" class="text-center">Presensi App</h1>
  			</v-col>
  			<v-col cols=12>
  				<h2 class="text-center">{{ user.name }}</h2>
  				<h2 class="text-center mt-2">{{ user.studentID }}</h2>
  				<h4 class="text-center mt-2">{{ decodedString }}</h4>
  			</v-col>
        <v-col cols=12>
        	<v-text-field
			      label="API IP"
			      v-model="ipAddress"
			    ></v-text-field>
        </v-col>
  			<v-col cols=12 class="mb-2">
  				<v-btn @click="auth()" color="red" dark block>
  					AUTHENTICATE NOW
  				</v-btn>
  			</v-col>
  			<v-col cols=12>
  				<v-btn @click="scan()" :disabled="user.authenticated != true" color="primary" block>
  					SCAN QR
  				</v-btn>
  			</v-col>
  		</v-row>
  	</v-container>

  	<v-dialog
      v-model="scanMode"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar
          dark
          color="primary"
        >
          <v-btn
            icon
            dark
            @click="scanMode = false"
          >
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>Scan QR Mode</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn
              dark
              text
              @click="scanMode = false"
            >
              Done
            </v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <!-- <v-list
          three-line
          subheader
        >
          <v-subheader>User Controls</v-subheader>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title>Content filtering</v-list-item-title>
              <v-list-item-subtitle>Set the content filtering level to restrict apps that can be downloaded</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title>Password</v-list-item-title>
              <v-list-item-subtitle>Require password for purchase or use password to restrict purchase</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <v-divider></v-divider>
        <v-list
          three-line
          subheader
        >
          <v-subheader>General</v-subheader>
          <v-list-item>
            <v-list-item-action>
              <v-checkbox v-model="notifications"></v-checkbox>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>Notifications</v-list-item-title>
              <v-list-item-subtitle>Notify me about updates to apps or games that I downloaded</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-action>
              <v-checkbox v-model="sound"></v-checkbox>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>Sound</v-list-item-title>
              <v-list-item-subtitle>Auto-update apps at any time. Data charges may apply</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-action>
              <v-checkbox v-model="widgets"></v-checkbox>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>Auto-add widgets</v-list-item-title>
              <v-list-item-subtitle>Automatically add home screen widgets</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list> -->
        <v-card-text>
	        <qrcode-stream v-if="scanMode" @decode="onDecode"></qrcode-stream>
        </v-card-text>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="authMode"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar
          dark
          color="primary"
        >
          <v-btn
            icon
            dark
            @click="deactivate()"
          >
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>Authenticate Face</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn
              dark
              text
              @click="deactivate()"
            >
              Done
            </v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <v-card-text class="pt-2">
        	<!-- <video class="mt-2" width="100%" height="100%" ref="video" controls autoplay name="media">
        		<source src="https://stream.mux.com/XRqHFNqz3ZOx8FzqrGUNNUUz1m01Cm3jK/high.mp4?token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InQ5UHZucm9ZY0hQNjhYSmlRQnRHTEVVSkVSSXJ0UXhKIn0.eyJleHAiOjE2MDk4OTc5MzksImF1ZCI6InYiLCJzdWIiOiJYUnFIRk5xejNaT3g4RnpxckdVTk5VVXoxbTAxQ20zaksifQ.DHoeZXTz6qtLE3u7Xe0iArWou4-W-8_iojAytKzCOEATVSIM_MwWKvbX1_zunssvZDNXl5yYaQBShhP8r5tsJBs8uu0mB2W12zPqbO3G_z88y-_1w-1BM8ae-ABm1gFIGhfK_1LHHmPJgMjsODXZfuWzD3P7iXkkLztori862o9LYFn0iLtiXjJT_EtMT0nDSgfO20N0XiGEy4WQp2y9ZErlVS3tWs61NUrA7YCzX2Ng9qhpwxsFoORaMt0X42tBcB3Gs9GnKbvltSQJEDPIHT_DC1bhFHM3S2o1GjvwH-sjFaOeikOdw-_tiqWuh3cbCJkWjIBBSuisyT9QOoXt3g" type="video/mp4">
        	</video> -->
        	<video ref="camera" :width="366" :height="337.5" autoplay></video>
        	<canvas ref="canvas"></canvas>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader'
import axios from 'axios'

export default {
  name: 'Home',
  components: {
  	QrcodeStream
  },
  data() {
  	return {
      ipAddress: '127.0.0.1',
  		scanMode: false,
  		authMode: false,
  		video: null,
  		predictions: [],
  		classifier: null,
  		status: '',
      decodedString: '',
	  	user: {
	  		studentID: null,
	  		name: null,
        authenticated: false
	  	}
  	}
  },
  watch: {
    '$route'() {
      const getDataByLocalStorage = (data = []) => {
        return JSON.parse(data) || {};
      }

      this.user = getDataByLocalStorage(localStorage.getItem('user'))
    }
  },
  mounted() {
		// this.classifier = await ml5.objectDetector('cocossd', this.modelReady)

    const getDataByLocalStorage = (data = []) => {
      return JSON.parse(data) || {};
    }

    this.user = getDataByLocalStorage(localStorage.getItem('user'))

    console.log(this.user);

    // this.$router.push('/auth');

    if(!this.user.studentID) {
      this.$router.push('/auth');
    }

  	console.log('mounted file')
  },
  // 
  methods: {
  	createCameraElement() {
      const videoConstraints = {
        facingMode: 'user'
      };

		  const constraints = (window.constraints = {
		    audio: false,
		    video: videoConstraints,
		  });

		  navigator.mediaDevices
		    .getUserMedia(constraints)
		    .then(stream => {
		      this.$refs.camera.srcObject = stream;
		      this.video = this.$refs.camera
		    })
		    .catch(error => {
		      alert("May the browser didn't support or there is some errors.");
		  });

		},
		stopCameraStream() {
		  let tracks = this.$refs.camera.srcObject.getTracks();

		  tracks.forEach(track => {
		    track.stop();
		  });
		},
  	modelReady() {
  		console.log('model loaded')

  		this.status = 'Model Loaded'

  		this.classifyVideo()
  	},
  	classifyVideo() {

  		this.classifier.detect(this.video, this.gotResult)
  	},
  	gotResult(err, results) {

  		let canvas = this.$refs.canvas

  		const ctx = canvas.getContext('2d')

  		canvas.width = 366
  		canvas.height = 337.5

  		ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height)

  		const font = '16px sans-serif'
  		
  		ctx.font = font
  		ctx.textBaseline = 'top'
  		ctx.drawImage(this.$refs.camera, 0, 0, canvas.width, canvas.height)

  		if(results) {
  			this.predictions = results

  			results.forEach(prediction => {
  				const x = prediction.x
  				const y = prediction.y
  				const width = prediction.width
  				const height = prediction.height

  				ctx.strokeStyle = "#00FFFF"
  				ctx.lineWidth = 2
  				ctx.strokeRect(x, y, width, height)

  				ctx.fillStyle = "#00FFFF"
  				const textWidth = ctx.measureText(prediction.label).width
  				const textHeight = parseInt(font, 10)
  				ctx.fillRect(x, y, textWidth + 4, textHeight + 4)

  			})

  			results.forEach(prediction => {
  				const x = prediction.x
  				const y = prediction.y

  				ctx.fillStyle = "#000000"
  				ctx.fillText(prediction.label, x, y)

          if(prediction.label === this.user.studentID || prediction.label === 'person') {
            this.user.authenticated = true
            this.deactivate()
          }
  			})

        console.log(results);

  			if(results[0].label === this.user.studentID || results[0].label === 'person') {
          this.user.authenticated = true
  				this.deactivate()
  			} else {
	  			this.classifyVideo()
  			}

  		}
  	},
  	scan() {
  		console.log('Aktifkan fitur kamera')
  		this.scanMode = true
  	},
  	auth() {
  		console.log('Aktifkan autentikasi')
  		this.authMode = true

  		if(!this.authMode) {
		    this.authMode = false;
		    this.stopCameraStream();
		  } else {
		    this.authMode = true;
		    try {
		    	this.createCameraElement();
          const API_DATA = 'https://teachablemachine.withgoogle.com/models/uzZX1Jefp/model.json';
		    	this.classifier = ml5.objectDetector(API_DATA, this.modelReady)
		    	
		    	console.log(this.classifier)
		    } catch (e) {
		    	console.error(e)
		    }

		  }

  	},
  	deactivate() {
  		this.stopCameraStream();
      this.user.authenticated = true
  		this.authMode = false
  	},
    async takePresensi(kunci) {
      kunci = kunci.replace(`b'`, '')
      kunci = kunci.replace(`'`, '')

      const headers = {
        'Content-Type': 'application/json'
      }

      return await axios.post(`http://${this.ipAddress}/presensi-cerdas/api/hadir/${this.user.studentID}`, {
        kunci
      }, {
        headers: headers
      }).then(res => res.data);
    },
  	async onDecode(decodedString) {
  		try {
	      const {
	        imageData,    // raw image data of image/frame
	        content,      // decoded String
	        location      // QR code coordinates
	      } = await decodedString

	      // ...
	      console.log(decodedString)

	      this.decodedString = 'Loading ga ya ?';

	      if(decodedString === '') {
	      	console.log('tidak akan di proses')
	      } else {

			  result = await this.takePresensi(decodedString).then(res => res)

			  if(result.status == 'Success') {
				this.decodedString = "Berhasil Presensi"
				this.scanMode = false
			  } else {
				this.decodedString = result
				this.scanMode = false
			  }
	      }

	    } catch (error) {
	      // ...
	      console.error(error)
	    }
  	}
  },
  updated() {
    // this.$nextTick(function () {
    //   console.log('updated file')
    
    //   const getDataByLocalStorage = (data = []) => {
    //     return JSON.parse(data) || {};
    //   }

    //   this.user = getDataByLocalStorage(localStorage.getItem('user'))

    //   if(!this.user.authenticated) {
    //     this.$router.push('/auth');
    //   }
    // })
    console.log('updated file')
  }
}
</script>
