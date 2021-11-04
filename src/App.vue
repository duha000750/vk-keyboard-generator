<template>
    <div id="main">
        <h2 id="title">Генератор клавиатуры</h2>

        <div id="keyboard">
            <div class="keyboard__row" v-for="(row,rowIndex) in keyboard" :key="rowIndex">
                <div class="keyboard__row__buttons">
                    <KeyboardButton
                        v-for="(button, buttonIndex) in row"
                        :key="buttonIndex"
                        :color="button.color"
                        :text="button.text"
                        :isSelected="rowIndex === activeButtonIndex[0] && buttonIndex === activeButtonIndex[1]"
                        @click="changeSelectedButton(rowIndex, buttonIndex)"
                    />
                </div>

                <div class="keyboard__row__add-button" @click="addButton(rowIndex)">+</div>
            </div>

        </div>

        <div>
            <button @click="addRow">Добавить строку</button>
        </div>

        <div id="keyboard-form">
            <div class="form-item">
                <label for="keyboard-form__text" class="keyboard-form__label">Текст кнопки</label>
                <input type="text" id="keyboard-form__text" class="keyboard-form__input" v-model="activeButtonText">
            </div>

            <div class="form-item">
                <label class="keyboard-form__label">Цвет кнопки</label>
                <div id="color-radio-group">
                    <div class="keyboard-form__color-item">
                        <input type="radio" name="button-color" v-model="activeButtonColor" id="button-color-radio-primary" value="primary" @change="changeColorButton">
                        <label class="button-color-label-primary" for="button-color-radio-primary"></label>
                    </div>

                    <div class="keyboard-form__color-item">
                        <input type="radio" name="button-color" v-model="activeButtonColor" id="button-color-radio-secondary" value="secondary" @change="changeColorButton">
                        <label class="button-color-label-secondary" for="button-color-radio-secondary"></label>
                    </div>

                    <div class="keyboard-form__color-item">
                        <input type="radio" name="button-color" v-model="activeButtonColor" id="button-color-radio-positive" value="positive" @change="changeColorButton">
                        <label class="button-color-label-positive" for="button-color-radio-positive"></label>
                    </div>

                    <div class="keyboard-form__color-item">
                        <input type="radio" name="button-color" v-model="activeButtonColor" id="button-color-radio-negative" value="negative" @change="changeColorButton">
                        <label class="button-color-label-negative" for="button-color-radio-negative"></label>
                    </div>
                </div>
            </div>

            <div class="form-item">
                <label for="keyboard-form__text" class="keyboard-form__label">Payload (полезная нагрузка)</label>
                <input type="text" id="keyboard-form__payload" class="keyboard-form__input" v-model="activeButtonPayload">
            </div>

            <div class="form-item">
                <button
                    style="display: inline-flex; align-items: center; justify-content: center; cursor: pointer;"
                    @click="deleteButton"
                >
                    <img src="icons/x.svg" alt="" style="width: 25px; margin-right: 10px;">
                    <span>Удалить кнопку</span>
                </button>
            </div>

            <div class="form-item">
                <label style="margin-top: 10px; cursor: pointer;">
                    <input type="checkbox" v-model="oneTime">
                    <span>Скрывать клавиатуру после первого использования</span>
                </label>
            </div>

        </div>


        <div id="keyboard-code">
            <textarea disabled>{{keyboardCode}}</textarea>
        </div>

    </div>
</template>

<script>

import KeyboardButton from "@/components/KeyboardButton";

export default {
    name: 'App',
    components: {
        KeyboardButton
    },

    data() {
        return {

            activeButtonIndex: [0,0],

            activeButtonText: '',
            activeButtonColor: 'primary',
            activeButtonPayload: '',
            oneTime: false, //скрывать ли клавиатуру после первого использования

            defaultButton: {
                text: 'кнопка',
                color: 'primary',
                payload: ''
            },

            keyboard: [[]]
        }
    },

    watch: {
        activeButtonText: function () {
            this.activeButton.text = this.activeButtonText;
        },
        activeButtonPayload: function () {
            this.activeButton.payload = this.activeButtonPayload;
        }
    },

    computed: {
        activeButton: function() {
            return this.keyboard[this.activeButtonIndex[0]][this.activeButtonIndex[1]];
        },
        keyboardCode: function () {
            let keyboardObj = {
                one_time: this.oneTime,
                buttons: [],
            };

            for (let i=0; i<this.keyboard.length;i++) {
                keyboardObj.buttons.push([]);
                for(let j=0;j<this.keyboard[i].length;j++) {
                    const button = {
                        color: this.keyboard[i][j].color,
                        action: {
                            type: 'text',
                            payload: this.keyboard[i][j].payload,
                            label: this.keyboard[i][j].text,
                        },
                    };
                    keyboardObj.buttons[i].push(button);
                }
            }

            return JSON.stringify(keyboardObj, null, 4);

        },
        buttonsCount: function () {
            let buttonsCount = 0;
            for(let i=0; i<this.keyboard.length;i++) {
                for(let j=0;j<this.keyboard[i].length;j++) {
                    buttonsCount++;
                }
            }
            return buttonsCount;
        }
    },

    methods: {
        changeSelectedButton: function (i,j) {
            this.activeButtonIndex = [i,j];
            this.activeButtonText = this.activeButton.text;
            this.activeButtonColor = this.activeButton.color;
            this.activeButtonPayload = this.activeButton.payload;
        },

        addButton: function (rowIndex) {
            if (this.keyboard[rowIndex].length === 5 || this.buttonsCount === 40) {
                return false;
            }

            let buttonObj = {};
            Object.assign(buttonObj, this.defaultButton);
            this.keyboard[rowIndex].push(buttonObj);

        },

        addRow: function () {
            if (this.keyboard.length === 10 || this.buttonsCount === 40) {
                return false;
            }

            this.keyboard.push([]);
            const lastRowIndex = this.keyboard.length - 1;
            this.addButton(lastRowIndex);

        },

        changeColorButton: function () {
            this.activeButton.color = this.activeButtonColor;
            console.log(this.keyboard);
        },

        deleteButton: function () {

            if (this.keyboard.length === 1 && this.keyboard[0].length === 1) {
                return false;
            }

            this.keyboard[this.activeButtonIndex[0]].splice(this.activeButtonIndex[1], 1);

            if (this.keyboard[this.activeButtonIndex[0]].length === 0) {
                this.keyboard.splice(this.activeButtonIndex[0], 1);
            }

            this.activeButtonIndex = [0,0];
        }
    },

    mounted() {
        this.addButton(0);
    }

}
</script>

<style lang="scss">

:root {
    --button-primary-background: #5181b8;
    --button-primary-color: #fff;

    --button-secondary-background: #dae2ea;
    --button-secondary-color: #55677d;

    --button-positive-background: #4bb34b;
    --button-positive-color: #fff;

    --button-negative-background: #e64646;
    --button-negative-color: #fff;
}

#app {
    font-family: sans-serif;
}


#main {
    display: flex;
    flex-direction: column;
    align-items: center;

}

#keyboard-code {
    margin-top: 10px;
}

#keyboard-code textarea {
    width: 522px;
    height: 200px;
}

#keyboard {


    .keyboard__row {
        display: flex;
        justify-content: flex-start;
        align-items: center;
        width: 522px;


        .keyboard__row__buttons {
            display: flex;
            flex-basis: calc(100% - 40px);
        }

        .keyboard__row__add-button {
            padding: 6px;
            background: #ccc;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-weight: bold;
            font-size: 14px;
            width: 30px;
            height: 30px;
            box-sizing: border-box;
            margin-left: 10px;
            cursor: pointer;
        }

    }
}

#keyboard-form {
    display: flex;
    flex-direction: column;
    margin-top: 30px;


    .form-item {
        display: flex;
        flex-direction: column;
        margin-top: 10px;
    }

    .keyboard-form__label {
        text-align: center;
        margin-bottom: 5px;
    }


    .keyboard-form__input {
        padding: 5px;
        border-radius: 5px;
        border: 1px solid;
        background: #dde9ff;
    }
}

#color-radio-group {
    display: flex;
    justify-content: center;

    .keyboard-form__color-item {
        margin-right: 5px;
    }

    input[name="button-color"] {
        display: none;

        &+label {
            display: block;
            width: 30px;
            height: 30px;
            cursor: pointer;
            box-sizing: border-box;
            border: 2px solid rgba(0,0,0,0);
        }

        &:checked+label {
            border-color: #000;
        };
    }

    .button-color-label-primary {
        background: var(--button-primary-background);
    }

    .button-color-label-secondary {
        background: var(--button-secondary-background);
    }

    .button-color-label-positive {
        background: var(--button-positive-background);
    }

    .button-color-label-negative {
        background: var(--button-negative-background);
    }

}


</style>
