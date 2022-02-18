<template>
    <div id="app">
        <div class="twt-page">
            <nav>TWT</nav>
            <div class="twt-body">
                <div class="twt-about">About</div>
                <div class="twt-profile">
                    <router-view></router-view>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "Index",
};
</script>

<style>
body {
    margin: 0;
}

nav {
    background: #c9ab4d;
    color: white;
    height: 40px;
    border: 2px groove #c9ab4d;
    border-top: 0;
    border-radius: 0 0 8px 8px;
}

.twt-page {
    max-width: 791px;
    margin: auto;
}

.twt-body {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
}

.twt-profile {
    width: 632px;
    box-shadow: 1px 0px rgba(0, 0, 0, 0.2), -1px 0px rgba(0, 0, 0, 0.2);
}
</style>
