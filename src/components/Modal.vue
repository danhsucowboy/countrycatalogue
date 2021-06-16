<template>
    <transition>
        <div class="modal-mask">
            <div class="modal-wrapper">
                <div class="modal-container">
                    <div>
                        <h3>
                            {{countries[0].name}}
                        </h3>
                    </div>
                    <div>
                        <ul>
                            <li v-for="(item, name) in otherData" :key="name">
                                {{name}}: {{item}}
                            </li>
                        </ul>
                    </div>
                    <button class="modal-button" @click="$emit('close')">Close</button>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
export default {
  name: 'Modal',
  props: {
      countries:{
            type: Array,
            required: true
      }
  },
  data() {
      return{
           otherData:{},
      };
  },
  methods: {
      showOtherData(){
          this.otherData = Object.assign({},this.countries[0]);
          ['name', 'alpha2Code', 'alpha3Code', 'nativeName', 'altSpellings', 'callingCodes'].forEach(e => delete this.otherData[e]);
      },
  },
  mounted() {
    this.showOtherData();
  },
}
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  display: flex;
  flex-direction: column;
  width: 40vw;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-button{
    width: 5vw;
    margin: 0px auto;
}
</style>