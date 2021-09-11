<template>
  <div id="app" class="m-4">
    <div class="title">
      <h3>Microphone</h3>
    </div>
    <div class="row m-2">
      <div class="col-md-4">
        <button
          type="button"
          class="btn btn-outline-info btn-sm"
          @click="countNumber"
        >
          Add count
        </button>
      </div>
      <div class="col-md-8">Count: {{ counter }}</div>
    </div>
    <div class="row">
      <div class="col-md-4">
        <h3>Microphone:</h3>
        <button
          type="button"
          class="btn btn-outline-info btn-sm"
          @click="startMic"
          :disabled="showSuccess"
        >
          Start
        </button>
        <button
          type="button"
          class="btn btn-outline-info btn-sm"
          @click="stopMic"
        >
          Stop
        </button>
      </div>
      <div v-show="showSuccess" class="alert alert-success alert-dismissible fade show" role="alert">
        <strong>Connected</strong>
        <button type="button" class="close" data-dismiss="alert" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="col-md-8">
        <h2><b>Output:</b></h2>
        <button
          type="button"
          class="btn btn-outline btn-sm"
          @click="clearMessage"
        >
          Clear Data output
        </button>
        <div id="output">{{ message }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import recognizeMicrophone from 'watson-speech/speech-to-text/recognize-microphone';

export default {
  name: "App",
  components: {},
  data() {
    return {
      showSuccess: false,
      counter: 0,
      message: '',
      messageObject: {},
    };
  },
  methods: {
    clearMessage() {
      this.message = '';
      this.messageObject = {};
    },
    countNumber() {
      this.counter += 1;
    },
    getMessage() {
      return Object.values(this.messageObject).join(' ');
    },
    startMic() {
      this.showSuccess = false;
      console.log("startMic");
      fetch('http://localhost:3000/api/speech-to-text/token')
      .then((response) => {
          return response.json();
      }).then((token) => {
        this.showSuccess = true;
        const options= {
          // token,
          ...token,
          objectMode: true, // send objects instead of text
          format: false, // optional - performs basic formatting on the results such as capitals an periods
          wordConfidence: true,
        };
        console.log('options', options);

        var stream = recognizeMicrophone(options);
        console.log('stream', stream);

        stream.on('data',(data) => {
          console.log('data', data);
          console.log('data.result_index', data.result_index);
          data.results.map(res => {
            res.alternatives.map(al => {
              console.log('al.transcript', al.transcript);
              // this.message = this.message + al.transcript;
              // avoid duplicate massage
              this.messageObject[data.result_index] = al.transcript;
              this.message = this.getMessage();
            });
          });
        });

        stream.on('error',(err) => {
            console.log('stream:ERROR', err);
        });

      }).catch((error) => {
          console.log('fetch:ERROR', error);
      });
    },
    stopMic() {
      console.log("stopMic");
      this.startMic.bind(this);
    },
  },
  created() {
    console.log('Created......');
  },
};
</script>

<style scoped>
#output {
  font-style: inherit;
  font-size: x-large;
}
</style>
