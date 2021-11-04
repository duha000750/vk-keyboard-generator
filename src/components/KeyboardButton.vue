<template>
    <div class="keyboard__button" :class="[{selected: isSelected}, color]">{{text}}</div>
</template>

<script>
export default {
    name: "KeyboardButton",
    props: ['text', 'color', 'isSelected']
}
</script>

<style lang="scss" scoped>

    .keyboard__button {
        border-radius: 6px;
        padding: 6px 12px;
        cursor: pointer;
        border: 2px solid rgba(0,0,0,0);
        flex-basis: 100%;
        margin: 5px;
        box-sizing: border-box;
        font-size: 12.5px;
        text-align: center;
        overflow: hidden;
        font-family: -apple-system, BlinkMacSystemFont, Roboto, Open Sans, Helvetica Neue, 'Noto Sans Armenian', 'Noto Sans Bengali', 'Noto Sans Cherokee', 'Noto Sans Devanagari', 'Noto Sans Ethiopic', 'Noto Sans Georgian', 'Noto Sans Hebrew', 'Noto Sans Kannada', 'Noto Sans Khmer', 'Noto Sans Lao', 'Noto Sans Osmanya', 'Noto Sans Tamil', 'Noto Sans Telugu', 'Noto Sans Thai', sans-serif;
    }

    .selected {
        border-color: rgba(0,0,0,1);
    }

    .primary {
        background: var(--button-primary-background);
        color: var(--button-primary-color);
    }

    .secondary {
        background: var(--button-secondary-background);
        color: var(--button-secondary-color);
    }

    .positive {
        background: var(--button-positive-background);
        color: var(--button-positive-color);
    }

    .negative {
        background: var(--button-negative-background);
        color: var(--button-negative-color);
    }
</style>