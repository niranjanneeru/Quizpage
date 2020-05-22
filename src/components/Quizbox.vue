<template>
    <div class="question-box">
        <b-jumbotron>
            <template v-slot:lead>
                {{question.question | filterf}}
            </template>

            <hr class="my-4">
            <b-list-group>
                <b-list-group-item id="list"
                                   v-for="(answer,index) in shuffledAnswers" :key="index"
                                   @click.prevent="selectedAnswer(index)"
                                   v-bind:class="answerClass(index)">
                    {{answer | filterf}}
                </b-list-group-item>
            </b-list-group>
            <b-button variant="primary"
                      @click="submitAnswer"
                      :disabled="selectedIndex === null || answered"
            >Submit
            </b-button>
            <b-button @click="next" variant="success" >Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>

    import _ from 'lodash'
    export default {
        props: {
            'question': Object,
            next: Function,
            increment: Function
        },
    filters:{
            filterf(value){
                value = value.split('&quot;').join('"')
                value = value.split('&amp;').join('&')
                value = value.split('&#039;').join('\'')
                return value
            }
    },
        data() {
            return {
                selectedIndex: null,
                shuffledAnswers: [],
                answered: false,
                correctIndex: null
            }
        },
        watch: {
            question: {
                immediate: true,
                handler() {
                    this.answered = false
                    this.selectedIndex = null
                    this.shuffleAnswers()
                }
            }
        }, methods: {
            selectedAnswer(index) {
                this.selectedIndex = index
            },
            answerClass(index) {
                let answeredClass = ''
                if (!this.answered && this.selectedIndex === index) {
                    answeredClass = 'selected'
                } else if (this.answered && this.correctIndex === index) {
                    answeredClass = 'correct'
                } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                    answeredClass = 'incorrect'
                }
                return answeredClass
            },
            submitAnswer() {
                let isCorrect = false
                if (this.selectedIndex === this.correctIndex) {
                    isCorrect = true
                }
                this.answered = true
                this.increment(isCorrect)
            },
            shuffleAnswers() {
                let answers = [...this.question.incorrect_answers, this.question.correct_answer]
                this.shuffledAnswers = _.shuffle(answers)
                this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer)
            }
        }
    }
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
    }

    #list:hover {
        background: #eeeeee;
        cursor: pointer;
    }

    .selected {
        background-color: lightblue;
    }

    .correct {
        background-color: green;
    }

    .incorrect {
        background-color: red;
    }

    .btn {
        margin: 0px 5px;
    }
</style>