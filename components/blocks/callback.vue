<template>
    <section class="callback wrapper">
        <h2 class="callback__title title-h2">Обратная связь</h2>
        <p class="callback__text text-big">Мы свяжемся с вами в течение <span class="text-bodily">15 минут</span>!</p>

        <div class="callback-container">
            <div class="callback__image">
                <div class="callback__image-mask">
                    <img 
                        class="callback__image-img" 
                        src="@/assets/sprites/woman-call.svg" 
                        alt="woman-star.svg"
                    >
                </div>
            </div>

            <form class="callback__form">
                <input class="callback__form-input" type="text" placeholder="ФИО">
                <input class="callback__form-input" type="text" placeholder="Телефон">
                <input class="callback__form-input" type="text" placeholder="Почта">
                <input class="callback__form-input" type="text" placeholder="Организация">
                <div class="callback__form-buttons">
                    <button
                        @click="(e) => {e.preventDefault(); selectedOption = 'tel'}"
                        :class="{ active: selectedOption === 'tel' }"
                        class="callback__form-button"
                    >
                        Позвонить
                    </button>
                    <button
                        @click="(e) => {e.preventDefault(); selectedOption = 'sms'}"
                        :class="{ active: selectedOption === 'sms' }"
                        class="callback__form-button"
                    >
                        Написать
                    </button>
                </div>
                <button class="callback__form-submit">
                    Получить обратную связь
                </button>
            </form>
        </div>
    </section>
</template>

<script setup>
    const selectedOption = ref('tel')
</script>