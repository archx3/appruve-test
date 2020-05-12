<template>
  <section class="calender-widget">
    <section class="head">
      <div class="nav-buttons">
        <button class="p-year" @click="previous('year')">
          <img src="https://image.flaticon.com/icons/png/128/25/25257.png" alt="">
        </button>
        <button class="p-month" @click="previous()">
          <img src="https://image.flaticon.com/icons/png/128/566/566011.png" alt="">
        </button>
      </div>
      <div class="mon-year-sel">
        <span class="mon" @click="openMonthSelector"> {{monthsArray[month]}}</span><span
        class="year" @click="openYearSelector">{{year}}</span>
      </div>
      <div class="nav-buttons right">
        <button class="n-month" @click="next()">
          <img src="https://image.flaticon.com/icons/png/128/566/566011.png" alt="">
        </button>
        <button class="n-year" @click="next('year')">
          <img src="https://image.flaticon.com/icons/png/128/25/25257.png" alt="">
        </button>
      </div>
    </section>
    <section class="body calendar">
      <div class="sexy-table full-cal">
        <table>
          <thead>
          <tr>
            <th v-for="(day, i) in daysArray" :key="i">{{getShortLabel(day)}}</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(week, weekIndex) in getCalendar" :key="weekIndex">
            <td v-for="(day, dayIndex) in week" :key="dayIndex"
                :class="{today : day.today, selected : isInDateInSelected(day, dayIndex, weekIndex), dumb : day.dumb}"
                @click="toggleSelect(day, dayIndex, weekIndex)">
              <div>{{day.date}}</div>
            </td>
          </tr>
          </tbody>
        </table>
      </div>
      <div v-if="monthSelectorOpen" class="sexy-table mon-cal">
        <div v-for="(month, i) in monthsArray" :key="i" class="mon" :class="{selected : selectedMonth === i}"
             @click="setMonth(i)">{{month}}
        </div>
      </div>
      <div v-if="yearSelectorOpen" class="sexy-table year-cal">
        <div v-for="(year, i) in getYearSelectorYears" :key="i" class="mon" :class="{selected : selectedYear === year}"
             @click="setYear(year)">{{year}}
        </div>
      </div>
    </section>
    <section class="selected-dates" v-show="showSelected">
      <p v-for="(date, i) in datesCache" :key="i" :style="{textAlign : 'left'}" >{{date}}</p>
    </section>
    <button class="show-all-dates" @click="showSelected = !showSelected">Toggle Show Selected Dates</button>
    <section class="foot">
      <p>Copyright &copy; <a href="http://kobina.me">Kobina G. Koomson</a> {{thisYear}} </p>
    </section>
  </section>

</template>

<script>
  export default {
    name: "Calendar",
    components: {},
    props: {
      monthYear: {
        type: Array,
        default: () => {
          let dateToday = new Date();
          return [dateToday.getFullYear(), dateToday.getMonth()]
        } // let's set the the date to today's date if not passed
      },

    },
    data () {
      return {
        today: new Date(),
        selectedMonth: undefined,
        selectedYear: undefined,
        daysArray: ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "saturday"],
        monthsArray: ["January", "February", "March", "April", "May", "June", "July", "August", "September",
          "October", "November", "December"],
        monthSelectorOpen: false,
        yearSelectorOpen: false,
        showSelected : false,
        selectedDates: [],
        selectedRange: [],
        datesCache: [],
      }
    },
    computed: {
      firstDay () {
        return (new Date(this.year, this.month)).getDay()
      },
      month () {
        return this.selectedMonth !== undefined ? this.selectedMonth : this.thisMonth
      },
      year () {
        return this.selectedYear || this.thisYear
      },
      thisMonth () {
        return this.today.getMonth();
      },
      thisYear () {
        return this.today.getFullYear()
      },
      /**
       * How many day are in a month
       */
      daysInMonth () {
        return 32 - new Date(this.year, this.month, 32).getDate();
      },
      daysLastMonth () {
        if (this.month === 1) {
          return 32 - new Date(this.year - 1, 1, 32).getDate();
        }
        return 32 - new Date(this.year, this.selectedMonth - 1, 32).getDate();
      },
      getSelectedDates () {
        return this.selectedDates.map((virtualDate) => {
          return `${this.year}-${this.month + 1}-${virtualDate.day.date}`
        });
      },
      getCalendar: {
        get: function () {
          let month = this.month, year = this.year, firstDay = this.firstDay;

          let calenderTable = []; // body of the calendar

          // creating all cells
          let date = 1;
          for (let i = 0; i < 6; i++) {
            // creates a table week
            let week = []; // will contain our days

            // creating individual day Array-Elements (month table cells), filing them up with data.
            let daysInMonth = this.daysInMonth;
            for (let j = 0; j < 7; j++) {
              if (i === 0 && j < firstDay) {
                week.push({ date: this.daysLastMonth + (j - 2), dumb: true });
              } else if (date > daysInMonth) {
                break;
              } else {
                if (date === this.today.getDate() && year === this.today.getFullYear() && month === this.today.getMonth()) {
                  week.push({ date, today: true, selected: false })
                } else {
                  week.push({ date, selected: false })
                }
                date++;
              }
            }
            calenderTable.push(week)
          }

          return calenderTable;
        },
        set: function (value, dayIndex, weekIndex) {
          this.getCalendar[value.weekIndex][value.dayIndex] = value;
          this.$set(this.getCalendar[value.weekIndex], value.dayIndex, value);
          /* this.getCalendar.forEach(week => {
            week.forEach(day => {
              console.log(day)
            })
          }) */
        }
      },
      getYearSelectorYears () {
        let year = this.selectedYear, years = [];
        for (let i = 1; i <= 12; i++) {
          years.push(((-1 * i) + 7) + year) // magic function: y = (-x + 7)
        }
        return years.reverse()
      }
    },
    watch : {
      getSelectedDates : function (dates)
      {
        if (dates.length > 0) {
          this.datesCache = dates
        }
      }
    },
    methods: {
      /**
       * Returns the short form of a given date unit.
       * E.g [getShortLabel("Monday", 3), getShortLabel("Monday")]
       * //-> ["Mon", "M"]
       * @param label
       * @param length
       * @returns {string}
       */
      getShortLabel (label, length = 1) {
        return label.substr(0, length)
      },
      clearSelected () {
        this.selectedDates = [];
      },
      setMonth (month) {
        this.selectedMonth = month;
        this.monthSelectorOpen = false;
        this.clearSelected();
      },
      setYear (year) {
        this.selectedYear = year;
        this.yearSelectorOpen = false;
        this.clearSelected();
      },
      openYearSelector () {
        this.monthSelectorOpen = false;
        this.yearSelectorOpen = !this.yearSelectorOpen;
      },
      openMonthSelector () {
        this.yearSelectorOpen = false;
        this.monthSelectorOpen = !this.monthSelectorOpen;
      },
      setMonthAndYear (month, year) {
        this.selectedMonth = month;
        this.selectedYear = year;
        this.clearSelected();
      },
      next (dateUnit = 'month') {
        this.monthSelectorOpen = false;
        this.yearSelectorOpen = false;
        if (dateUnit !== "month") {
          this.selectedYear = this.selectedYear + 1;
        } else {
          if (this.selectedMonth !== 11) {
            this.selectedMonth = (this.selectedMonth + 1);
          } else {
            this.selectedYear = this.selectedYear + 1;
            this.selectedMonth = (this.selectedMonth + 1) % 12;
          }
        }
        this.clearSelected();
        /* console.log(this.selectedMonth,this.selectedYear) */
      },
      previous (dateUnit = 'month') {
        this.monthSelectorOpen = false;
        this.yearSelectorOpen = false;
        if (dateUnit !== "month") {
          this.selectedYear = this.selectedYear - 1
        } else {
          if (this.selectedMonth === 0) {
            this.selectedMonth = 11;
            this.selectedYear -= 1;
          } else {
            this.selectedMonth -= 1
          }
        }
        this.clearSelected();
      },
      goTo (value, dateUnit = 'month',) {
        if (dateUnit !== "month") {
          this.selectedYear = parseInt(value, 10)
        } else {
          this.selectedMonth = parseInt(value, 10)
        }
        this.clearSelected();
      },

      /**
       *
       * @param day {number}
       * @param dayIndex {number}
       * @param weekIndex
       * @returns {boolean|number}
       */
      isInDateInSelected (day, dayIndex, weekIndex) {
        for (let i = 0, len = this.selectedDates.length; i < len; i++) {
          let selectedDate = this.selectedDates[i];
          if (selectedDate.day.date === day.date && selectedDate.dayIndex === dayIndex && selectedDate.weekIndex === weekIndex) {
            return i
          }
        }
        return false;
      },
      toggleSelect (day, dayIndex, weekIndex, event) {
        const found = this.isInDateInSelected(day, dayIndex, weekIndex);
        if (found === false) {
          // day.selected = true;
          // this.$set(day, 'selected', true )
          /*  this.getCalendar.forEach(week => {
             week.forEach(day => {
               console.log(day)
             })
           }); */
          this.selectedDates.push({ day, dayIndex, weekIndex })
        } else {
          this.selectedDates.splice(found, 1)
        }
      }
    },
    mounted () {
      this.selectedMonth = this.month;
      this.selectedYear = this.year;
    },
    updated () {
      console.log('updated', this.getSelectedDates);
    },
    created () {
    },
    destroyed () {
    }
  }
</script>

<style lang="scss">
  body {
    background: #20262E;
    padding: 20px;
    font-family: Helvetica;
    box-sizing: border-box;
  }

  .selected-dates{
    padding: 15px;
    border: 1px solid #8b9494;
    background: rgba(17, 29, 29, 0.2);
    p {
      margin: 0;
      font-size: 1.2em;
    }
  }

  #app {
    background: #fff;
    /* border-radius: 4px; */
    padding: 20px;
    transition: all 0.2s;
  }

  section {
    &.calender-widget {
      /* width: 250px; */
      display: inline-block;
      min-height: 25px;
      padding: 5px;
      overflow: hidden;
      position: relative;
      border-right: 1px solid rgba(204, 204, 204, 0.89);
    }

    .head {
      height: 50px;
      width: 100%;
      padding: 10px;
      display: flex;
      flex-direction: row;
      flex-wrap: nowrap;
      justify-content: space-between;
      text-align: center;
      background: rgba(204, 204, 204, 0.89);

      div {
        display: inline-block;
      }

      .nav-buttons {
        width: 22%;
        display: flex;
        justify-content: space-between;

        button {
          background: #ffffff;
          /*border: none;*/
          height: 30px;
          width: 30px;
          text-align: center;
          -webkit-border-radius: 50px;
          -moz-border-radius: 50px;
          border-radius: 50px;
          padding: 5px;
          /*padding-top:5px;*/
          cursor: pointer;
          border: 2px solid transparent;

          img {
            width: 10px;
          }

          &:hover {
            border-color: #0078ff;
            background: #ffb700;
          }
        }

        &.right {
          button {
            img {
              rotate: 180deg;
            }
          }
        }
      }

      .mon-year-sel {
        /*margin: 0 auto;*/
        padding-top: 6px;

        span {
          padding: 5px 12px;
          cursor: pointer;
          border-radius: 15px;
          border: 2px solid transparent;

          &:hover {
            border-color: #0078ff;
            background: #98C8FF;
          }
        }
      }
    }

    .body {
      position: relative;
      overflow: hidden;
    }

    .mon-cal, .year-cal {
      position: absolute;
      top: 0;
      width: 100%;
      height: 100%;
      background: #ffffff;
      display: flex;
    }

    .mon-cal, .year-cal {
      flex-direction: row;
      flex-wrap: wrap;
      justify-content: space-around;
      padding: 10px 5px;
      text-align: center;
      font-weight: 600;
      border: 1px solid #ccc;

      div {
        width: 30%;
        height: 45px;
        padding: 12px 3px;
        cursor: pointer;
        -webkit-border-radius: 25px;
        -moz-border-radius: 25px;
        border-radius: 25px;
        border: 2px solid transparent;

        &.selected {
          /*background: #ffb700;*/
          /*border-color: #0078ff;*/
          border-color: #000;
        }

        &:hover {
          border-color: #0078ff;
        }
      }
    }
  }

  table {
    border: 1px solid #ccc;
    background: #ffffff;

    thead {
      background: #0078FF;
    }

    tr {
      min-height: 34px;
    }

    td, th {
      text-align: center;
      padding: 15px;
    }

    th {
      font-weight: 600;
      color: #fff;
      padding: 15px;
    }

    td {
      cursor: pointer;
      padding: 8px;

      div {
        pointer-events: none;
        padding: 7px;
        border-radius: 50%
      }

      &:hover > div {
        color: #ffffff;
        background: #98C8FF;
        font-weight: 600;
      }

      &.dumb {
        cursor: default;

        > div {
          color: #ccc;
        }
      }

      &.dumb:hover > div {
        color: #ccc;
        background: #FFF;
      }

      &.selected > div {
        background: #0078FF;
        color: #fff;
        font-weight: 600;
      }

      &.today {

        > div {
          background: #D9FF00;
          color: #000;
          font-weight: 600;
        }

        &.selected > div {
          background: #ffb700;
          color: #fff;
        }
      }
    }

    &.full-cal {
    }
  }

  .foot p {
    padding: 7px;
    text-align: center;
  }
</style>
