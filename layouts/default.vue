<template>
    <div id="app">
        <slot />
    </div>
</template>

<script setup>

    const route = useRoute()
    const pagesSEO = {
        'index': {
            title: 'НаноСофт',
            keywords: 'keywords',
            description: 'description'
        }
    }
    const pageSEO = pagesSEO[route.name]        

    useHead({
        title: pageSEO.title,
        meta: [
            { name: 'keywords', content: pageSEO.keywords },
            { charset: "utf-8" },
            { name: "viewport", content: "width=device-width, initial-scale=1" },
            { property: "og:title", content: pageSEO.title, },
            { property: "og:url", content: `https://dontraffic.ru${route.name != 'index' ? '/' + route.name : ''}` },
            { name: 'description', content: pageSEO.description },
            { property: "og:description", content: pageSEO.description },
            { property: "og:image", content: 'https://dontraffic.ru/images/wallpapperLink.jpg', }
        ]
    })

</script>