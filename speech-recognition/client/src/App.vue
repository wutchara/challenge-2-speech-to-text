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
      <div class="col-md-8">
        <h3>Output:</h3>
        <div id="output">Open your browser's console to view the output.</div>
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
      counter: 0,
      stream: null,
    };
  },
  methods: {
    countNumber() {
      this.counter += 1;
    },
    startMic() {
      console.log("startMic");
      fetch('http://localhost:3000/api/speech-to-text/token')
      .then((response) => {
        console.log('response', response);
          return response.text();
      }).then((token) => {
        console.log('token', token);

        var stream = recognizeMicrophone(Object.assign(token, {
            objectMode: true, // send objects instead of text
            format: false // optional - performs basic formatting on the results such as capitals an periods
        }));

        stream.on('data',(data) => {
          console.log(data);
        });

        stream.on('error',(err) => {
            console.log('stream', err);
        });

      }).catch((error) => {
          console.log('fetch', error);
      });
    },
    stopMic() {
      console.log("stopMic");
      // this.stream && this.stream.stop.bind(this.stream);
    },
  },
  created() {
    console.log('Created......Try to to create stram');
    // this.axios({
    //   method: "get",
    //   url: "http://127.0.0.1:3000/users",
    //   responseType: "arraybuffer",
    // })
    //   .then((response) => {
    //     // console.log('response', response);

    //     var enc = new TextDecoder("utf-8");
    //     return JSON.parse(enc.decode(response.data));
    //   })
    //   .then((res) => {
    //     console.log("res", res);
    //     this.users = (res?.data || []).reverse();
    //   })
    //   .catch((err) => {
    //     console.error(err);
    //   })
    //   .finally(() => {
    //     console.log("......... Load data compleated .........");
    //     this.isLoading = false;
    //   });
  },
};
</script>
