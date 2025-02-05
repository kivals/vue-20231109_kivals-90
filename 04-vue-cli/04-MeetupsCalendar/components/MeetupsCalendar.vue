<template>
  <div class="calendar-view">
    <div class="calendar-view__controls">
      <div class="calendar-view__controls-inner">
        <button
          @click="decrementMonth"
          class="calendar-view__control-left"
          type="button"
          aria-label="Previous month"
        ></button>
        <div class="calendar-view__date">{{ calendarTitle }}</div>
        <button
          @click="incrementMonth"
          class="calendar-view__control-right"
          type="button"
          aria-label="Next month"
        ></button>
      </div>
    </div>

    <div class="calendar-view__grid">
      <div
        v-for="({value, isInactive, events}, index) in model"
        :key="index"
        :class="['calendar-view__cell', {'calendar-view__cell_inactive': isInactive}]"
        tabindex="0"
      >
        <div class="calendar-view__cell-day">{{ value }}</div>
        <div class="calendar-view__cell-content">
          <a
            v-for="{id, title} in events"
            :key="id"
            :href="`/meetups/${id}`"
            class="calendar-event"
          >
            {{ title }}
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MeetupsCalendar',

  props: {
    meetups: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      date: new Date(),
    };
  },
  computed: {
    calendarTitle() {
      return this.date.toLocaleDateString(navigator.language, {
        month: 'long',
        year: 'numeric',
      });
    },
    model() {
      let model = [];
      const firstDateOfMonth = new Date(this.date.getFullYear(), this.date.getMonth(), 1);
      const lastDateOfMonth = new Date(this.date.getFullYear(), this.date.getMonth() + 1, 0);

      let countPrevMonthDays = firstDateOfMonth.getDay() === 0 ? 6 : firstDateOfMonth.getDay() - 1;
      let countNextMonthDays = lastDateOfMonth.getDay() === 0 ? 0 : 7 - lastDateOfMonth.getDay();

      // формирование дней предыдущего месяца
      for (let i = countPrevMonthDays; i > 0; i--) {
        model.push({
          value: new Date(
            firstDateOfMonth.getFullYear(),
            firstDateOfMonth.getMonth(),
            firstDateOfMonth.getDate() - i,
          ).getDate(),
          isInactive: true,
        });
      }
      // формирование дней текущего месяца
      for (let i = 1; i <= lastDateOfMonth.getDate(); i++) {
        const monthDateMs = Date.UTC(this.date.getFullYear(), this.date.getMonth(), i);
        const events = this.meetups.filter((m) => m.date === monthDateMs);
        model.push({
          value: i,
          events,
        });
      }
      // формирование дней следующего месяца
      for (let i = 1; i <= countNextMonthDays; i++) {
        model.push({
          value: new Date(
            lastDateOfMonth.getFullYear(),
            lastDateOfMonth.getMonth(),
            lastDateOfMonth.getDate() + i,
          ).getDate(),
          isInactive: true,
        });
      }
      return model;
    },
  },
  methods: {
    incrementMonth() {
      this.date = new Date(this.date.getFullYear(), this.date.getMonth() + 1);
    },
    decrementMonth() {
      this.date = new Date(this.date.getFullYear(), this.date.getMonth() - 1);
    },
  },
};
</script>

<style scoped>
.calendar-view {
}

.calendar-view__controls {
  text-align: center;
  font-weight: 700;
  font-size: 24px;
  line-height: 1;
  color: var(--blue);
  background-color: var(--blue-extra);
  padding: 24px;
  display: flex;
  justify-content: center;
}

.calendar-view__controls-inner {
  max-width: 325px;
  width: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  text-transform: capitalize;
}

.calendar-view__controls-inner button {
  border: none;
  padding: 0;
}

.calendar-view__control-left,
.calendar-view__control-right {
  width: 30px;
  height: 30px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  cursor: pointer;
  transition: 0.3s all;
  background: url('@/assets/icons/icon-pill-active.svg') left center no-repeat;
  background-size: cover;
}

.calendar-view__control-left:hover,
.calendar-view__control-right:hover {
  opacity: 0.8;
}

.calendar-view__control-right {
  transform: rotate(180deg);
}

.calendar-view__grid {
  display: grid;
  grid-template-columns: repeat(1, 1fr);
}

.calendar-view__grid {
  border: 1px solid var(--grey);
  border-bottom: none;
}

.calendar-view__cell {
  position: relative;
  height: auto;
  padding: 6px 8px;
  background-color: var(--white);
  color: var(--grey-8);
  font-weight: 400;
  font-size: 16px;
  line-height: 24px;
  border-bottom: 1px solid var(--grey);
  border-left: 1px solid var(--grey);
  text-align: right;
}

.calendar-view__cell.calendar-view__cell_inactive {
  background-color: var(--grey-light);
}

@media all and (max-width: 767px) {
  .calendar-view__cell:nth-child(5n + 1) {
    border-left: none;
  }
}

@media all and (min-width: 767px) {
  .calendar-view__grid {
    grid-template-columns: repeat(7, 1fr);
  }

  .calendar-view__cell {
    height: 144px;
  }

  .calendar-view__cell:nth-child(7n + 1) {
    border-left: none;
  }
}

.calendar-event {
  --max-lines: 2;
  --line-height: 16px;

  display: block;
  text-align: left;
  text-decoration: none;
  text-overflow: ellipsis;
  overflow: hidden;
  font-size: 14px;
  font-weight: 600;
  line-height: var(--line-height);
  color: var(--white);
  padding: 4px 6px;
  border-radius: 2px;
  background-color: var(--blue);
  margin-top: 4px;
}

@media all and (min-width: 767px) {
  .calendar-event {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    max-height: calc(var(--max-lines) * var(--line-height) + 6px);
  }
}
</style>
