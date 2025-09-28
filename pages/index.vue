<template>
    <header 
        :class="[
            'headerFixed',
            isScrolled ? 'headerFixed--visible' : ''
        ]"
    >
        <div class="headerFixed-wrapper">
            <img class="headerFixed__logo" src="@/assets/sprites/NSLogoGorizont.svg" alt="Логотип">

            <div class="headerFixed__menu-container">
                <!-- <svg class="headerFixed__menu-svg">
                  <use xlink:href="@/assets/sprites/sprite.svg#menu"></use>
                </svg> -->

                <img class="headerFixed__menu-svg" @click="changeVisibleMenu()" src="@/assets/sprites/menu.svg" alt="menu.svg">

                <div 
                    class="headerFixed__menu"
                    :class="{ 'headerFixed__menu--open': isMenuOpen }"
                >
                    <button class="headerFixed__menu-btn" @click="scrollToAnchor('company'); changeVisibleMenu()">О компании</button>
                    <button class="headerFixed__menu-btn" @click="scrollToAnchor('advantages'); changeVisibleMenu()">Услуги</button>
                    <button class="headerFixed__menu-btn" @click="scrollToAnchor('cases'); changeVisibleMenu()">Кейсы</button>
                    <button class="headerFixed__menu-btn" @click="scrollToAnchor('footer'); changeVisibleMenu()">Контакты</button>
                </div>
            </div>
        </div>

        <div class="headerFixed__shadow"></div>
    </header>

    <Header></Header>

    <BlocksCompany></BlocksCompany>

    <BlocksResult></BlocksResult>

    <BlocksAdvantages></BlocksAdvantages>

    <!-- <BlocksSlidecompan></BlocksSlidecompan> -->

    <BlocksCompanys></BlocksCompanys>

    <BlocksCallback></BlocksCallback>

    <Footer></Footer>

</template>

<script setup>
    import { ref, onMounted, onBeforeUnmount } from 'vue'

    const isScrolled = ref(false)
    const isMobile = ref(false)
    const isMenuOpen = ref(false)

    function handleScroll() {
        isScrolled.value = window.scrollY > window.innerHeight/2
    }

    function handleResize() {
        isMobile.value = window.innerWidth <= 425
    }

    function changeVisibleMenu() {
        isMenuOpen.value = !isMenuOpen.value
    }

    onMounted(() => {
        handleResize()
        window.addEventListener('scroll', handleScroll)
        window.addEventListener('resize', handleResize)
    })

    onBeforeUnmount(() => {
        window.removeEventListener('scroll', handleScroll)
        window.removeEventListener('resize', handleResize)
    })

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