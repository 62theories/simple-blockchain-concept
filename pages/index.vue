<template>
  <div
    style="min-height: 100vh;max-width: 960px;margin: auto;overflow-y: auto;background-color: beige;padding: 15px;"
  >
    <h4 style="text-align:center;">simple uxto</h4>
    <div
      style="
    height: 50px;
    margin-top: 30px;
    border: 1px solid black;
    background-color: aqua;
    display: flex;
    justify-content: center;
    align-items: center;
    /* padding-left: 15px; */
"
    >
      <div>
        BLOCK REWARD = 10
      </div>
    </div>
    <div
      style="
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
"
    >
      <div
        style="
    border: 1px solid black;
    flex-basis: 30%;
    display: flex;
    justify-content: center;
    align-items: center;
    /* margin-left: 15px; */
    margin-right: 15px;
    background-color: white;
    padding: 15px;
"
      >
        <div>
          <p>A WALLET</p>
          <p>SUM NOW = {{ calculateSumFromBlockChain("A") }}</p>
          <p>SUM AFTER MINE = {{ calculateSumBeforeSpend("A") }}</p>
          <div style="margin-top: 15px;">
            <input
              v-model="walletA.payToB"
              placeholder="pay to B"
              type="number"
              min="0"
            />
          </div>
          <div style="margin-top: 15px;">
            <input
              v-model="walletA.fee"
              placeholder="fee"
              type="number"
              min="0"
            />
          </div>
          <div style="margin-top: 15px;">
            <button
              style="width: 100%;"
              @click="aPayToB(walletA.payToB, walletA.fee)"
            >
              pay
            </button>
          </div>
        </div>
      </div>
      <div
        style="
    border: 1px solid black;
    flex-basis: 30%;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-left: 15px;
    margin-right: 15px;
    background-color: white;
"
      >
        <div>
          <p>MINER</p>
          <p>SUM = {{ calculateSumFromBlockChain("miner") }}</p>
          <button style="width: 100%;" @click="mine">mine</button>
        </div>
      </div>
      <div
        style="
    flex-basis: 30%;
    border: 1px solid black;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-left: 15px;
    background-color: white;
    padding: 15px;
"
      >
        <div>
          <p>B WALLET</p>
          <p>SUM = {{ calculateSumFromBlockChain("B") }}</p>
          <p>SUM AFTER MINE = {{ calculateSumBeforeSpend("B") }}</p>
          <div style="margin-top: 15px;">
            <input
              v-model="walletB.payToA"
              placeholder="pay to A"
              type="number"
              min="0"
            />
          </div>
          <div style="margin-top: 15px;">
            <input
              v-model="walletB.fee"
              placeholder="fee"
              type="number"
              min="0"
            />
          </div>
          <div style="margin-top: 15px;">
            <button
              style="width: 100%;"
              @click="bPayToA(walletB.payToA, walletB.fee)"
            >
              pay
            </button>
          </div>
        </div>
      </div>
    </div>
    <div
      style="
    margin-top: 40px;
    display: flex;
"
    >
      <div
        style="
    width: 300px;
    
    margin-right: 15px;
"
      >
        <div
          style="    border: 1px solid black;
    padding: 15px;
    background-color: aqua;"
        >
          FEE FOR NEXT BLOCK = {{ feeSum }}
        </div>
        <div
          style="border: 1px solid black; margin-top: 15px; text-align: center; padding: 5px;"
        >
          <h4>MEMPOOL</h4>
          <div
            v-for="(transaction, transactionIndex) in memPool"
            :key="`memPooltransaction${transactionIndex}`"
            style="border: 1px solid black;
    padding: 15px;
    margin-top: 15px;
    background-color: aqua;
    "
          >
            <h5>transaction</h5>
            <div style="    border: 1px solid black; background-color: white;">
              input
              <div
                v-for="(input, inputIndex) in transaction.input"
                :key="`memPoolinput${inputIndex}`"
              >
                index = {{ input.index }}
              </div>
            </div>
            <div
              style="
    border: 1px solid black;
    margin-top: 15px; 
    padding: 15px;
    background-color: white;
    "
            >
              output
              <div
                v-for="output in transaction.output"
                :key="`memPooloutput${output.index}`"
                style="border: 1px solid black; margin-top: 15px;"
              >
                <p>index = {{ output.index }}</p>
                <p>from = {{ output.from }}</p>
                <p>to = {{ output.to }}</p>
                <p>amount = {{ output.amount }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div
        style="
    flex-grow: 1;
    border: 1px solid black;
    padding: 15px;
"
      >
        <div>
          <div
            v-for="(block, index) in blockChain"
            :key="`block${index}`"
            style="padding: 15px;
    border: 1px solid black;
    text-align: center;
    margin-top: 20px;
    background-color: white;
"
          >
            <h4>block number {{ index }}</h4>
            <div
              v-for="(transaction, transactionIndex) in block"
              :key="`transaction${transactionIndex}`"
              style="border: 1px solid black;
    padding: 15px;
    margin-top: 15px;
    background-color: aqua;
    "
            >
              <h5>transaction</h5>
              <div
                style="    border: 1px solid black; background-color: white;"
              >
                input
                <div
                  v-for="input in transaction.input"
                  :key="`input${input.index}`"
                >
                  index = {{ input.index }}
                </div>
              </div>
              <div
                style="
    border: 1px solid black;
    margin-top: 15px; 
    padding: 15px;
    background-color: white;
    "
              >
                output
                <div
                  v-for="output in transaction.output"
                  :key="`output${output.index}`"
                  style="border: 1px solid black; margin-top: 15px;"
                >
                  <p>index = {{ output.index }}</p>
                  <p>from = {{ output.from }}</p>
                  <p>to = {{ output.to }}</p>
                  <p>amount = {{ output.amount }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      blockReward: 10,
      walletA: {
        payToB: null,
        fee: null
      },
      walletB: {
        payToA: null,
        fee: null
      },
      feeSum: 0,
      indexToAssign: 7,
      memPool: [],
      blockChain: [
        [
          {
            input: [],
            output: [
              {
                index: 0,
                from: "system",
                to: "miner",
                amount: 10
              }
            ]
          }
        ],
        [
          {
            input: [],
            output: [
              {
                index: 1,
                from: "system",
                to: "miner",
                amount: 10
              }
            ]
          },
          {
            input: [
              {
                index: 0
              }
            ],
            output: [
              {
                index: 2,
                from: "miner",
                to: "A",
                amount: 8
              },
              {
                index: 3,
                from: "miner",
                to: "miner",
                amount: 2
              }
            ]
          }
        ],
        [
          {
            input: [],
            output: [
              {
                index: 4,
                from: "system",
                to: "miner",
                amount: 10.1
              }
            ]
          },
          {
            input: [
              {
                index: 2
              }
            ],
            output: [
              {
                index: 5,
                from: "A",
                to: "B",
                amount: 2
              },
              {
                index: 6,
                from: "A",
                to: "A",
                amount: 1.9
              }
            ]
          }
        ]
      ]
    };
  },
  methods: {
    calculateSumFromBlockChain(wallet) {
      return this.blockChain.reduce((acc, block) => {
        let sum = 0;
        block.forEach(transaction => {
          transaction.output.forEach(({ to, index, amount }) => {
            if (to === wallet) {
              if (
                !this.blockChain.find(block =>
                  block.find(transaction =>
                    transaction.input.find(inputObj => inputObj.index === index)
                  )
                )
              ) {
                sum += +amount;
              }
            }
          });
        });
        return +acc + +sum;
      }, 0);
    },
    calculateSumBeforeSpend(wallet) {
      return [...this.blockChain, [...this.memPool]].reduce((acc, block) => {
        let sum = 0;
        block.forEach(transaction => {
          transaction.output.forEach(({ to, index, amount }) => {
            if (to === wallet) {
              if (
                ![...this.blockChain, [...this.memPool]].find(block =>
                  block.find(transaction =>
                    transaction.input.find(inputObj => inputObj.index === index)
                  )
                )
              ) {
                sum += +amount;
              }
            }
          });
        });
        return +acc + +sum;
      }, 0);
    },
    addToPool(from, to, amount, fee = 0) {
      try {
        const totalAmount = +amount + +fee;
        if (this.calculateSumBeforeSpend(from) < totalAmount) {
          alert("not enough balance");
          throw new Error("not enough balance");
        }
        console.log([...this.blockChain, [...this.memPool]]);
        const inputWithAmountLeft = [
          ...this.blockChain,
          [...this.memPool]
        ].reduce(
          (acc, block) => {
            let ret = acc;
            block.forEach(transaction => {
              transaction.output
                .filter(output => {
                  if (output.to === from) {
                    if (
                      ![...this.blockChain, [...this.memPool]].find(block =>
                        block.find(transaction =>
                          transaction.input.find(
                            input => input.index === output.index
                          )
                        )
                      )
                    ) {
                      return true;
                    } else {
                      return false;
                    }
                  } else {
                    return false;
                  }
                })
                .forEach(output => {
                  if (output.amount >= acc.amountLeft) {
                    ret = {
                      memInput: [
                        ...ret.memInput,
                        {
                          index: output.index
                        }
                      ],
                      amountLeft: +ret.amountLeft - +output.amount
                    };
                  }
                });
            });
            return ret;
          },
          {
            memInput: [],
            amountLeft: amount
          }
        );
        const output = [
          {
            index: this.indexToAssign,
            from,
            to,
            amount
          },
          {
            index: this.indexToAssign + 1,
            from,
            to: from,
            amount:
              inputWithAmountLeft.memInput.reduce((acc, input) => {
                let ret = acc;
                [...this.blockChain, [...this.memPool]].forEach(block => {
                  block.forEach(transaction => {
                    transaction.output.forEach(output => {
                      if (input.index === output.index) {
                        ret = ret + +output.amount;
                      }
                    });
                  });
                });
                return ret;
              }, 0) - amount
          }
        ];
        this.indexToAssign = this.indexToAssign + 2;
        this.feeSum = +this.feeSum + +fee;
        this.memPool = [
          ...this.memPool,
          {
            input: inputWithAmountLeft.memInput,
            output
          }
        ];
      } catch (err) {
        throw err;
      }
    },
    aPayToB(amount, fee) {
      try {
        if (amount) {
          this.addToPool("A", "B", amount, fee);
          this.walletA.payToB = null;
          this.walletA.fee = null;
        }
      } catch (err) {
        console.log(err);
      }
    },
    bPayToA(amount, fee) {
      try {
        if (amount) {
          this.addToPool("B", "A", amount, fee);
          this.walletB.payToA = null;
          this.walletB.fee = null;
        }
      } catch (err) {
        console.log(err);
      }
    },
    mine() {
      this.blockChain = [
        ...this.blockChain,
        [
          {
            input: [],
            output: [
              {
                index: this.indexToAssign,
                from: "system",
                to: "miner",
                amount: +this.blockReward + +this.feeSum
              }
            ]
          },
          ...this.memPool
        ]
      ];
      this.memPool = [];
      this.feeSum = 0;
      this.indexToAssign = +this.indexToAssign + 1;
    }
  }
};
</script>
