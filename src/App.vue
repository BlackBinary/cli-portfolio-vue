<template>
  <div id="app">
    <div class="loader" v-if="!loaded">Please stand by, loading webconsole...</div>

    <div class="console" v-if="loaded" @click="focusInput()">
      <div class="line" v-for="(line, index) in oldLines" v-bind:key="index">
        <span v-html="line"></span>
      </div>
      <div class="line">
        <div class="host">{{ host }}</div>
        <label class="directory" for="directory">{{ directory }}</label>
        <div id="cmd">
          <span>{{ input }}</span>
          <div id="cursor"></div>
        </div>
        <input
          ref="consoleInput"
          id="directory"
          type="text"
          class="pointer"
          v-model="input"
          autofocus
          @keydown.enter="execute"
        >
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      loaded: false,
      user: "joost",
      hostname: "portfolio",
      location: "~",
      inputData: "",
      oldLines: [],
      motdLines: [
        "Welcome 1337 to the worlds most awesome web console",
        "I am currently trying to actually make this thing happen",
        "#####################################################",
        "###           This might actually work            ###",
        "###        also this is my first attempt at       ###",
        "###    ever creating something awesome with css   ###",
        "#####################################################"
      ],
      files: {
        userRoot: [
          'Photos', 'Portfolio', 'Private', 'Work'
        ]
      }
    };
  },
  computed: {
    host() {
      return `${this.user}@${this.hostname}:`;
    },
    directory() {
      return `${this.location}$`;
    },
    input: {
      get() {
        return this.inputData;
      },
      set(value) {
        this.inputData = value;
      }
    }
  },
  methods: {
    focusInput() {
      this.$refs.consoleInput.focus();
    },
    execute() {
      const output = this.command(this.inputData);
      this.oldLines.push(output);
      this.inputData = "";
    },
    command(input) {
      let cmdOutput = "";
      switch (input) {
        case "help":
          cmdOutput =
            "Welcome to the prompt. Available command are: whoami, whatsnext, ls, cd, explain";
          break;
        case "ls":
          if (this.location === '~') {
            cmdOutput = `<span class="color-white">${this.files.userRoot.join(',').replace(/,/g, '  ')}</span>`;
          }
          break;
        case "whoami":
          cmdOutput = `${this.user}`;
          break;
        case "clear":
          this.oldLines = [];
          break;
        default:
          cmdOutput = `Command '${input}' is not an available command`;
          break;
      }
      return cmdOutput;
    }
  },
  mounted() {
    this.motdLines.forEach(line => {
      this.oldLines.push(line);
    });
    setTimeout(() => {
      this.loaded = true;
    }, 500);
  }
};
</script>

<style>
* {
  margin: 0;
}
#app {
  font-family: "Inconsolata", monospace;
}
.loader {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  align-self: center;
}
.console {
  height: 100vh;
  width: 100vw;
  background: black;
}
.line {
  line-height: 1.1;
  font-size: 120%;
  color: greenyellow;
  display: flex;
  white-space: pre;
}
.host {
}
.pointer {
  background: #000;
  color: #fff;
  border: 0;
  font-family: "Inconsolata", monospace;
  font-size: 100%;
  line-height: 1;
}
.pointer:focus {
  outline: none;
}
.directory {
  color: teal;
}


#cmd {
  font-size: 100%;
  color: greenyellow;
  display: flex;
  align-items: center;
}
#cmd span {
  float: left;
  padding-left: 3px;
  white-space: pre;
}
#cursor {
  float: left;
  width: 7px;
  height: 15px;
  margin-left: 2px;
  background: white;
}
input {
  width: 0;
  height: 0;
  opacity: 0;
}

.color-white {
  color: white;
}
</style>
