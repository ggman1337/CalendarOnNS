<script lang="ts" setup>
import { ref, computed } from "nativescript-vue";

interface CalendarDay {
    label: string;
    dayNumber: number | null;
    year: number;
    month: number;
    isToday: boolean;
}

const today = new Date();
const todayYear = today.getFullYear();
const todayMonth = today.getMonth();
const todayDate = today.getDate();

const displayYear = ref<number>(todayYear);
const displayMonth = ref<number>(todayMonth);

const weekDayNames = ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"];

const monthNames = [
    "Январь",
    "Февраль",
    "Март",
    "Апрель",
    "Май",
    "Июнь",
    "Июль",
    "Август",
    "Сентябрь",
    "Октябрь",
    "Ноябрь",
    "Декабрь",
];

const monthLabel = computed<string>(() => {
    return monthNames[displayMonth.value] + " " + displayYear.value;
});

const isCurrentMonth = computed<boolean>(() => {
    return displayYear.value === todayYear && displayMonth.value === todayMonth;
});

const days = computed<CalendarDay[]>(() => {
    const year = displayYear.value;
    const month = displayMonth.value;

    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const daysInMonth = lastDay.getDate();

    let startWeekDay = firstDay.getDay();
    if (startWeekDay === 0) startWeekDay = 7;

    const result: CalendarDay[] = [];

    for (let i = 1; i < startWeekDay; i++) {
        result.push({
            label: "",
            dayNumber: null,
            year,
            month,
            isToday: false,
        });
    }

    for (let d = 1; d <= daysInMonth; d++) {
        const isToday =
            year === todayYear && month === todayMonth && d === todayDate;

        result.push({
            label: String(d),
            dayNumber: d,
            year,
            month,
            isToday,
        });
    }

    while (result.length % 7 !== 0) {
        result.push({
            label: "",
            dayNumber: null,
            year,
            month,
            isToday: false,
        });
    }

    return result;
});

function changeMonth(delta: number): void {
    let m = displayMonth.value + delta;
    let y = displayYear.value;

    if (m > 11) {
        m = 0;
        y++;
    } else if (m < 0) {
        m = 11;
        y--;
    }

    displayMonth.value = m;
    displayYear.value = y;
}

function changeYear(delta: number): void {
    displayYear.value = displayYear.value + delta;
}

function goToCurrentMonth(): void {
    displayYear.value = todayYear;
    displayMonth.value = todayMonth;
}
</script>

<template>
    <Page>
        <ActionBar title="Календарь" />

        <GridLayout rows="auto, auto, auto, auto" class="page">
            <GridLayout
                row="0"
                columns="auto,*,auto"
                rows="auto"
                class="header"
            >
                <Button
                    col="0"
                    text="‹"
                    @tap="changeMonth(-1)"
                    class="nav-button"
                />
                <StackLayout col="1" horizontalAlignment="center">
                    <Label :text="monthLabel" class="month-label" />
                    <GridLayout
                        columns="auto,auto,auto"
                        rows="auto"
                        class="year-row"
                    >
                        <Button
                            text="−"
                            col="0"
                            class="year-button"
                            @tap="changeYear(-1)"
                        />
                        <Label
                            :text="displayYear.toString()"
                            col="1"
                            class="year-label"
                        />
                        <Button
                            text="+"
                            col="2"
                            class="year-button"
                            @tap="changeYear(1)"
                        />
                    </GridLayout>
                </StackLayout>
                <Button
                    col="2"
                    text="›"
                    @tap="changeMonth(1)"
                    class="nav-button"
                />
            </GridLayout>

            <GridLayout
                row="1"
                columns="*,*,*,*,*,*,*"
                rows="auto"
                class="weekdays"
            >
                <Label
                    v-for="(day, i) in weekDayNames"
                    :key="i"
                    :col="i"
                    :text="day"
                    class="weekday-label"
                />
            </GridLayout>

            <GridLayout
                row="2"
                columns="*,*,*,*,*,*,*"
                rows="auto,auto,auto,auto,auto,auto"
                class="days-grid"
            >
                <Label
                    v-for="(day, index) in days"
                    :key="index"
                    :row="Math.floor(index / 7)"
                    :col="index % 7"
                    :text="day.label"
                    class="day-label"
                    :class="{
                        'day-empty': !day.label,
                        'day-today': day.isToday,
                    }"
                    horizontalAlignment="center"
                    verticalAlignment="center"
                />
            </GridLayout>

            <Button
                row="3"
                v-if="!isCurrentMonth"
                text="Текущий месяц"
                class="today-button"
                @tap="goToCurrentMonth"
            />
        </GridLayout>
    </Page>
</template>
