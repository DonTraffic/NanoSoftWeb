<template>
    <header class="header">
        <div class="header__menu">
            <button class="header__menu-btn" @click="scrollToAnchor('company')">О компании</button>
            <button class="header__menu-btn" @click="scrollToAnchor('advantages')">Услуги</button>
            <button class="header__menu-btn" @click="scrollToAnchor('cases')">Кейсы</button>
            <button class="header__menu-btn" @click="scrollToAnchor('footer')">Контакты</button>
        </div>

        <div class="header-container">
            <div class="header__subtitle">разработка / внедрение / поддержка</div>

            <img class="header__logo" src="@/assets/sprites/NSLogo.svg" alt="Логотип">

            <div class="header__title">
                <div class="header__title-container">
                    <p class="header__title-text--mobile title-h3">Экспертные решения для вашего бизнеса</p>
                    
                    <span class="header__title-text">Экспертные решения</span>
                    <div class="header__title-transfer">
                        <span class="header__title-transfer-item">1С</span>
                        <span class="header__title-transfer-item">CRM</span>
                        <span class="header__title-transfer-item">Telegram-боты</span>
                    </div>
                </div>

                <span class="header__title-text">для вашего бизнеса</span>
            </div>
        </div>
    </header>
</template>

<script setup>
    function scrollToAnchor(anchorId, headerOffset = 0) {
        if (process.server) return // на сервере не выполняем

        const element = document.getElementById(anchorId)
        if (!element) return

        const elementRect = element.getBoundingClientRect()
        const elementTop = elementRect.top + window.scrollY
        const elementHeight = elementRect.height
        const windowHeight = window.innerHeight

        const scrollPosition =
            elementTop - (windowHeight / 2) + (elementHeight / 2) - headerOffset

        window.scrollTo({
            top: scrollPosition,
            behavior: 'smooth'
        })
    }
</script>
