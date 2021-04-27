<template>
  <div class="bg-main">
    <div class="container">
      <div class="add-data-panel">
        <div class="text-center">
          <h2>add block</h2>
        </div>
        <div class="form-group">
          <label>data</label>
          <input v-model="newData" class="form-control" />
        </div>
        <div>
          <button @click="createNewBlock" class="btn btn-success btn-block">
            submit
          </button>
        </div>
      </div>
      <div class="blockchain-container">
        <div
          :class="`block-container ${block.isValid ? '' : 'red'}`"
          v-for="(block, index) in blockWithVerify"
          :key="`block${index}`"
        >
          <div class="form-group">
            <label>index</label>
            <input class="form-control" :value="block.index" disabled />
          </div>
          <div class="form-group">
            <label>data</label>
            <input
              @input="inputData($event, index)"
              class="form-control"
              :value="block.data"
            />
          </div>
          <div class="form-group">
            <label>previous_hash</label>
            <textarea
              class="form-control"
              :value="block.previous_hash"
              disabled
              rows="5"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import sha256 from "js-sha256";
export default {
  data() {
    return {
      blockchain: [
        {
          index: 1,
          data: "genesis block",
          previous_hash: "1"
        }
      ],
      newData: ""
    };
  },
  methods: {
    createNewBlock() {
      if (this.newData) {
        const newBlock = {
          index: this.blockchain.length + 1,
          data: this.newData,
          previous_hash: sha256(
            JSON.stringify(this.blockchain[this.blockchain.length - 1])
          )
        };
        this.blockchain = [...this.blockchain, newBlock];
        this.newData = "";
      }
    },
    inputData({ target: { value } }, index) {
      this.blockchain = this.blockchain.map((block, indexOfArr) => {
        if (indexOfArr === index) {
          return {
            ...block,
            data: value
          };
        } else {
          return {
            ...block
          };
        }
      });
    }
  },
  computed: {
    blockWithVerify() {
      let previous_hash = sha256(JSON.stringify(this.blockchain[0]));
      return this.blockchain.map((block, index) => {
        if (index === 0) {
          return {
            ...block,
            isValid: true
          };
        } else {
          if (block.previous_hash !== previous_hash) {
            previous_hash = sha256(JSON.stringify({ ...block, previous_hash }));
            return {
              ...block,
              isValid: false
            };
          } else {
            previous_hash = sha256(JSON.stringify({ ...block, previous_hash }));
            return {
              ...block,
              isValid: true
            };
          }
        }
      });
    }
  }
};
</script>

<style scoped>
.bg-main {
  background-color: #ffc996;
  min-height: 100vh;
  width: 100%;
  overflow-y: auto;
  padding-bottom: 15px;
}

.blockchain-container {
  display: flex;
  flex-wrap: wrap;
}
.block-container {
  width: 200px;
  height: fit-content;
  border: 1px solid black;
  padding: 15px;
  box-sizing: border-box;
  margin-right: 15px;
  margin-top: 15px;
  background-color: #9ede73;
}

.add-data-panel {
  background-color: #dcdcdc;
  padding: 15px;
  box-sizing: border-box;
  margin-top: 15px;
}

.red {
  background-color: tomato;
}
</style>
