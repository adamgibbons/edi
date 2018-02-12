<template>
  <div class="app">
    <div class="box">
      <div class="title">
        <strong>EDI</strong>
      </div>
      <textarea v-model="edi"></textarea>
    </div>
    <div class="box">
      <div class="title">
        <strong>PARSED EDI</strong>
      </div>
      <div class="line">
        <span class="element"
          v-for="(element, idx) in ISA"
          v-bind:key="idx"
          v-on:mouseover="activateElement(element)">
          <!-- <span class="key">{{Object.keys(element)[0]}}</span> -->
          <span class="value">{{Object.values(element)[0]}}</span>
        </span>
      </div>
      <div class="line">
        <span class="element"
          v-for="(element, idx) in GS"
          v-bind:key="idx"
          v-on:mouseover="activateElement(element)">
          <!-- <span class="key">{{Object.keys(element)[0]}}</span> -->
          <span class="value">{{Object.values(element)[0]}}</span>
        </span>
      </div>
      <div class="line">
        <span class="element"
          v-for="(element, idx) in ST"
          v-bind:key="idx"
          v-on:mouseover="activateElement(element)">
          <!-- <span class="key">{{Object.keys(element)[0]}}</span> -->
          <span class="value">{{Object.values(element)[0]}}</span>
        </span>
      </div>
      <div class="line">
        <span class="element"
          v-for="(element, idx) in AK1"
          v-bind:key="idx"
          v-on:mouseover="activateElement(element)">
          <!-- <span class="key">{{Object.keys(element)[0]}}</span> -->
          <span class="value">{{Object.values(element)[0]}}</span>
        </span>
      </div>
    </div>
    <div class="box">
      <p>active element:</p>
      <pre>{{activeElement}}</pre>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'
import spec from './spec.json'

export default {
  computed: {
    FA997 () {
      return this.edi.split('\n')
    },
    ISA () {
      return this.parseSegment('ISA', this.FA997[0])
    },
    GS () {
      return this.parseSegment('GS', this.FA997[1])
    },
    ST () {
      return this.parseSegment('ST', this.FA997[2])
    },
    AK1 () {
      const list = this.FA997.find((a) => {
        return a.indexOf('AK1') !== -1
      }).split('*').map((item, idx) => {
        return { [`AK10${idx}`]: item }
      })

      return _.zipWith(list, this.spec.AK1, (a, b) => {
        console.log(a, b)
        return Object.assign(a, b)
      })
    }
  },
  data () {
    return {
      spec,
      edi: `ISA*01*0000000000*01*0000000000*ZZ*ABCDEFGHIJKLMNO*ZZ*123456789012345*101127*1719*U*00400*000003438*0*P*>\nGS*FA*999999999*4405197800*20111206*1100*1*X*004010VICS\nST*997*0001\nAK1*PO*1421\nAK9*A*1*1*1\nSE*4*0001\nGE*1*1\nIEA*1*000000001`,
      activeElement: null
    }
  },
  methods: {
    parseSegment (segment, row) {
      const list = row.split('*').map((item, idx) => {
        return { [`${segment}${idx}`]: item }
      })

      return _.zipWith(list, this.spec[segment], (a, b) => {
        if (a && b) {
          return Object.assign(a, b)
        }
        return a || b
      })
    },
    activateElement (element) {
      this.activeElement = element
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .view-mode {
    background: #ddd;
    padding: 1em;
  }
  .edit-mode {

  }
  .element {
    border: 1px solid lightgray;
    padding: .33em;
    margin-right: .33em;
    font-family: monospace;
  }
  .element:hover {
    background-color: gray;
    color: white;
  }
  .line {
    line-height: 2em;
  }
  textarea {
    width: 100%;
    min-height: 10em;
  }
  .box {
    padding: 1em;
    margin: 1em 0;
    background: #eee;
  }
</style>
