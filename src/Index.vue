<template>
    <div id="app">
        <div class="twt-page">
            <div class="twt-header">
                <img src="/logo.png" />
            </div>
            <div class="twt-body">
                <div class="twt-about">
                    <a href="https://twtxt.readthedocs.io/en/latest/">Twtxt spec</a>
                    <a href="https://github.com/buckket/twtxt">Twtxt GitHub</a>
                    <a href="">Twtvk Github</a>
                </div>
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
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, Fira, Sans, "Droid Sans", "Helvetica Neue", sans-serif;
}

.twt-page {
    max-width: 791px;
    margin: auto;
}

.twt-header {
    background: rgb(201,171,77);
    background: linear-gradient(0deg, rgba(201,171,77,1) 0%, rgba(170,145,67,1) 100%);
    color: white;
    height: 40px;
    border: 2px groove #c9ab4d;
    border-top: 0;
    border-radius: 0 0 8px 8px;
    display: flex;
    flex-direction: row;
    align-items: center;
}

.twt-header > img {
    width: 140px;
}

.twt-body {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
}

.twt-about {
    display: flex;
    flex-direction: column;
    margin-top: 4px;
}

.twt-about > a {
    font-size: 11px;
    font-weight: normal;
    color: black;
    text-decoration: none;
    padding: 2px 0px;
}

.twt-about > a:hover {
    text-decoration: underline;
}

.twt-profile {
    width: 632px;
    box-shadow: 1px 0px rgba(0, 0, 0, 0.2), -1px 0px rgba(0, 0, 0, 0.2);
}
</style>
