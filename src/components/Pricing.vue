<template>
  <section id='app-'>
<!--    <h3 class="text-center font-weight-light heading pb-4">Pricing</h3>-->
    <div class='pricing pricing-palden'>
      <div v-for="(plan, i) in plans" class="pricing-item"
           :class="[{'pricing__item--featured' : plan.featured}, plan.name]"
           :key="i">
        <div class='pricing-deco'>
          <div class='pricing-price'>
            <span class='pricing-currency'>$ </span>
            <span class="amount">{{plan.price}}</span>
            <span class='pricing-period'>/ mo</span>
          </div>
          <h3 class='pricing-title'>{{plan.name}}</h3>
        </div>
        <ul class='pricing-feature-list'>
          <li v-for="(line, j) in plan.description" :key="j" class='pricing-feature'>{{line}}</li>
        </ul>
        <button :class="['pricing-action', plan.name]" @click="choosePlan(plan.id)">Choose plan</button>
      </div>
    </div>
  </section>
</template>

<script>
  import axios from 'axios';

  export default {
    name: "Pricing",
    components: {},
    props: [],
    data () {
      return {
        plans: [
          {
            id: '345345353465',
            name: 'bronze',
            price: '49.99',
            description: ['1 GB of data', 'Support at $25/hour', 'Limited cloud access']
          },
          {
            featured: true,
            id: '135457753535',
            name: 'silver',
            price: '59.99',
            description: [
              '   5 GB of data ',
              '    Support at $5/hour ',
              '    Full cloud access']
          },
          {
            id: '343453566555',
            name: 'gold',
            price: '99.99',
            description: [
              '10 GB of data',
              'Support at $5/hour',
              'Full cloud access']
          },
        ]
      }
    },
    computed: {},
    methods: {
      choosePlan (planId) {
        axios.post('http://url.xyz', {
          planId
        }, { timeout: 1500 })
      }
    },
    mounted () {
    },
    created () {
    },
    destroyed () {
    }
  }
</script>

<style lang="scss" scoped>
  $gold : #b08402;
  body {
    -webkit-font-smoothing: antialiased;
    font-family: 'Open Sans';
    font-style: condensed;
  }

  section {
    /*background: #647df9;*/
    /*color: #7a90ff;*/
    padding: 2em 0 8em;
    min-height: 100vh;
    position: relative;
    -webkit-font-smoothing: antialiased;
    font-family: 'Open Sans', sans-serif;
  }

  h3.heading {
    font-weight: 400;
    font-size: 3em;
  }

  .pricing {
    display: -webkit-flex;
    display: flex;
    -webkit-flex-wrap: wrap;
    flex-wrap: wrap;
    -webkit-justify-content: center;
    justify-content: center;
    width: 100%;
    margin: 0 auto 3em;

    &-item {
      position: relative;
      display: -webkit-flex;
      display: flex;
      -webkit-flex-direction: column;
      flex-direction: column;
      -webkit-align-items: stretch;
      align-items: stretch;
      text-align: center;
      -webkit-flex: 0 1 330px;
      flex: 0 1 330px;
    }

    &-action {
      color: inherit;
      border: none;
      background: none;

      &:focus {
        outline: none;
      }
    }
  }

  .pricing-feature-list {
    text-align: left;
  }

  .pricing-palden .pricing-item {
    font-family: 'Open Sans', sans-serif;
    cursor: default;
    color: #84697c;
    background: #fff;
    box-shadow: 0 0 10px rgba(46, 59, 125, 0.23);
    border-radius: 20px 20px 10px 10px;
    margin: 1em;
  }

  @media screen and (min-width: 66.250em) {
    .pricing-palden .pricing-item {
      margin: 1em -0.5em;
    }
    .pricing-palden .pricing__item--featured {
      margin: 0;
      z-index: 10;
      box-shadow: 0 0 20px rgba(46, 59, 125, 0.23);
    }
  }

  .pricing-palden {
    .pricing-deco {
      border-radius: 10px 10px 0 0;
      /*background: rgba(76, 70, 101, 0.99);*/
      padding: 4em 0 9em;
      position: relative;
    }

    .pricing-deco-img {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 160px;
    }

    .pricing-title {
      font-size: 0.75em;
      margin: 0;
      text-transform: uppercase;
      letter-spacing: 5px;
      color: #fff;
    }

    .deco-layer {
      -webkit-transition: -webkit-transform 0.5s;
      transition: transform 0.5s;
    }

    .pricing-item {
      &.gold {
        .pricing-deco {
          background-color: $gold;
        }
      }

      &.silver {
        .pricing-deco {
          background-color: silver;
        }
      }

      &.bronze {
        .pricing-deco {
          background-color: #CD7F32;
        }
      }

      &:hover {
        .deco-layer--1 {
          -webkit-transform: translate3d(15px, 0, 0);
          transform: translate3d(15px, 0, 0);
        }

        .deco-layer--2 {
          -webkit-transform: translate3d(-15px, 0, 0);
          transform: translate3d(-15px, 0, 0);
        }
      }
    }

    .icon {
      font-size: 2.5em;
    }

    .pricing-price {
      font-size: 5em;
      font-weight: 400;
      padding: 0;
      color: #fff;
      margin: 0 0 0.25em 0;
      line-height: 0.75;
    }

    .pricing-currency {
      font-size: 0.55em;
      vertical-align: middle;

    }

    .pricing-period {
      font-size: 0.15em;
      padding: 0 0 0 0.5em;

      font-style: italic;
    }

    .pricing__sentence {
      font-weight: bold;
      margin: 0 0 1em 0;
      padding: 0 0 0.5em;
    }

    .pricing-feature-list {
      margin: 0;
      padding: 0.25em 0 2.5em;
      list-style: none;
      text-align: center;
    }

    .pricing-feature {
      padding: 1em 0;
    }

    .pricing-action {
      font-weight: bold;
      margin: auto 3em 2em 3em;
      padding: 1em 2em;
      color: #fff;
      border-radius: 30px;
      background: #4d4766;
      -webkit-transition: background-color 0.3s;
      transition: background-color 0.3s;

      &.gold {
        background-color: $gold;
      }

      &.silver {
        background-color: silver;
      }

      &.bronze {
        background-color: #CD7F32;
      }

      &:hover, &:focus {

        background-color: #100A13;

      }
    }

  }

  .pricing-palden .pricing-item--featured .pricing-deco {
    padding: 5em 0 8.885em 0;
  }
</style>
