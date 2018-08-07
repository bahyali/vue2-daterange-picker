<template>
    <div class="reportRange-con">
        <div class="reportrange-text" @click="togglePicker">
            <slot name="input">
                <i class="glyphicon glyphicon-calendar fa fa-calendar"></i>&nbsp;
                <span>{{startText}} - {{endText}}</span>
                <div class="rr--button"><b class="caret"></b></div>
            </slot>
        </div>
        <transition name="slide-fade" mode="out-in">
            <div class="daterangepicker dropdown-menu ltr" :class="pickerStyles()" v-if="open"
                 v-on-clickaway="clickAway">

                <div class="calendar left">
                    <div class="daterangepicker_input hidden-xs">
                        <input class="input-mini form-control" type="text" name="daterangepicker_start"
                               :value="startText"/>
                        <i class="fa fa-calendar glyphicon glyphicon-calendar"></i>
                    </div>
                    <div class="calendar-table">
                        <calendar :monthDate="monthDate"
                                  :locale="locale"
                                  :start="start" :end="end"
                                  @nextMonth="nextMonth" @prevMonth="prevMonth"
                                  @dateClick="dateClick" @hoverDate="hoverDate"
                        ></calendar>
                    </div>
                </div>

                <div class="calendar right hidden-xs">
                    <div class="daterangepicker_input">
                        <input class="input-mini form-control" type="text" name="daterangepicker_end"
                               :value="endText"/>
                        <i class="fa fa-calendar glyphicon glyphicon-calendar"></i>
                    </div>
                    <div class="calendar-table">
                        <calendar :monthDate="nextMonthDate"
                                  :locale="locale"
                                  :start="start" :end="end"
                                  @nextMonth="nextMonth" @prevMonth="prevMonth"
                                  @dateClick="dateClick" @hoverDate="hoverDate"
                        ></calendar>
                    </div>
                </div>

                <calendar-ranges :canSelect="in_selection" @clickCancel="open=false" @clickRange="clickRange"
                                 @clickApply="clickedApply" :ranges="ranges" :locale="locale" :autoApply="autoApply">
                </calendar-ranges>
            </div>
        </transition>
    </div>
</template>

<script>
    import moment from 'moment'
    import Calendar from './Calendar.vue'
    import CalendarRanges from './CalendarRanges'
    import {nextMonth, prevMonth} from './util'
    import {mixin as clickaway} from 'vue-clickaway'

    export default {
        components: {Calendar, CalendarRanges},
        mixins: [clickaway],
        props: {
            localeData: {
                type: Object,
                default() {
                    return {}
                },
            },
            startDate: {
                default() {
                    return new Date()
                }
            },
            endDate: {
                default() {
                    return new Date()
                }
            },
            ranges: {
                type: Object,
                default() {
                    return {
                        'Today': [moment(), moment()],
                        'Yesterday': [moment().subtract(1, 'days'), moment().subtract(1, 'days')],
                        'This month': [moment().startOf('month'), moment().endOf('month')],
                        'This year': [moment().startOf('year'), moment().endOf('year')],
                        'Last week': [moment().subtract(1, 'week').startOf('week'), moment().subtract(1, 'week').endOf('week')],
                        'Last month': [moment().subtract(1, 'month').startOf('month'), moment().subtract(1, 'month').endOf('month')],
                    }
                }
            },
            opens: {
                type: String,
                default: 'center'
            },
            autoApply: {
                type: Boolean,
                default: true
            }
        },
        data() {
            let default_locale = {
                direction: 'ltr',
                format: moment.localeData().longDateFormat('L'),
                separator: ' - ',
                applyLabel: 'Apply',
                cancelLabel: 'Cancel',
                weekLabel: 'W',
                customRangeLabel: 'Custom Range',
                daysOfWeek: moment.weekdaysMin(),
                monthNames: moment.monthsShort(),
                firstDay: moment.localeData().firstDayOfWeek()
            }

            // let data = { locale: _locale }
            let data = {locale: {...default_locale, ...this.localeData}}

            data.monthDate = new Date(this.startDate)
            data.start = new Date(this.startDate)
            data.end = new Date(this.endDate)
            data.in_selection = false
            data.open = false

            // update day names order to firstDay
            if (data.locale.firstDay !== 0) {
                let iterator = data.locale.firstDay
                while (iterator > 0) {
                    data.locale.daysOfWeek.push(data.locale.daysOfWeek.shift())
                    iterator--
                }
            }
            return data
        },
        methods: {
            nextMonth() {
                this.monthDate = nextMonth(this.monthDate)
            },
            prevMonth() {
                this.monthDate = prevMonth(this.monthDate)
            },
            dateClick(value) {
                if (this.in_selection) {
                    this.in_selection = false
                    this.end = new Date(value)
                    if (this.start > this.end){
                        this.start = new Date(value)
                        this.in_selection = true
                    }

                } else {
                    this.in_selection = true
                    this.start = new Date(value)
                    this.end = new Date(value)
                }
            },
            hoverDate(value) {
                let dt = new Date(value)
                if (this.in_selection && dt > this.start)
                    this.end = dt
            },
            togglePicker() {
                this.open = !this.open
            },
            pickerStyles() {
                return {
                    'show-calendar': this.open,
                    opensright: this.opens === 'right',
                    opensleft: this.opens === 'left',
                    openscenter: this.opens === 'center'
                }
            },
            clickedApply() {
                this.open = false
                this.$emit('update', {startDate: this.start, endDate: this.end})
            },
            clickAway() {
                if (this.open) {
                    this.open = false
                }
            },
            clickRange(value) {
                this.start = new Date(value[0])
                this.end = new Date(value[1])
                this.monthDate = new Date(value[0])
                this.clickedApply()
            }
        },
        computed: {
            nextMonthDate() {
                return nextMonth(this.monthDate)
            },
            startText() {
                return this.start.toLocaleDateString()
            },
            endText() {
                return new Date(this.end).toLocaleDateString()
            }
        },
        watch: {
            startDate(value) {
                this.start = new Date(value)
            },
            endDate(value) {
                this.end = new Date(value)
            },
            in_selection(value) {
                if (this.autoApply === true && value === false)
                    this.clickedApply();
            }
        }
    }

</script>

<style>
    .reportRange-con {
        width: auto;
        position: relative;
        display: inline-block;
    }

    .rr--button {
        float: right;
    }

    .range_inputs {
        margin-bottom: 10px;
    }

    .reportrange-text {
        background: #fff;
        cursor: pointer;
        padding: 5px 10px;
        border: 1px solid #ccc;
        width: 100%;
        vertical-align: middle;
        /*display: flex;*/
        justify-content: space-between;
    }

    .daterangepicker.show-calendar {
        display: inline-flex;
        flex-direction: row;
    }

    .daterangepicker .ranges {
        width: 160px;
    }

    @media only screen and (min-device-width: 320px) and (max-device-width: 480px) {
        .daterangepicker.show-calendar {
            flex-direction: column;
        }

        .daterangepicker .ranges {
            width: 100%;
        }
    }

    .ranges {
        width: 130px;
    }

    .show-calendar {
        display: block;
        width: auto;
        margin-bottom: 30px;
    }

    div.daterangepicker.opensleft {
        top: 35px;
        right: 10px;
        left: auto;
    }

    div.daterangepicker.openscenter {
        top: 35px;
        right: auto;
        left: 50%;
        transform: translate(-50%, 0);
    }

    div.daterangepicker.opensright {
        top: 35px;
        left: 10px;
        right: auto;
    }

    /* Enter and leave animations can use different */
    /* durations and timing functions.              */
    .slide-fade-enter-active {
        transition: all .2s ease;
    }

    .slide-fade-leave-active {
        transition: all .1s cubic-bezier(1.0, 0.5, 0.8, 1.0);
    }

    .slide-fade-enter, .slide-fade-leave-to
        /* .slide-fade-leave-active for <2.1.8 */
    {
        transform: translateX(10px);
        opacity: 0;
    }

</style>
