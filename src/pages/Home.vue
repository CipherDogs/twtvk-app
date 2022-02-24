<template>
    <div>
        <div class="profile-name">{{ nick }}</div>
        <div class="profile">
            <div class="profile-left">
                <img :src="avatar" />
                <div class="profile-left-following">
                    <div class="profile-left-following-name">Following</div>
                    <div class="profile-left-following-count">{{ count_follow }} users</div>
                    <div class="profile-left-follows">
                        <a class="profile-left-follow" v-for="item in follow" v-bind:key="item.user" :href="`/?url=${item.link}`">
                            <img :src="item.avatar">
                            <p>{{ item.user }}</p>
                        </a>
                    </div>
                </div>
            </div>
            <div class="profile-right">
                <div class="profile-right-info">
                    <h2 class="profile-right-name">{{ nick }}</h2>
                    <table class="profile-right-about">
                        <tbody>
                            <tr>
                                <td>About:</td>
                                <td>{{ description }}</td>
                            </tr>
                            <tr>
                                <td>Link:</td>
                                <td>{{ url }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
                <div class="profile-right-posts">
                    <div class="profile-right-wall">Wall <span>{{ count_posts }} posts</span></div>
                    <div v-for="post in posts" v-bind:key="post.date" class="profile-right-post">
                        <img :src="avatar" />
                        <div>
                            <p>{{ nick }}</p>
                            <p v-html="post.text"></p>
                            <p>
                                {{
                                    new Date(post.date).toLocaleDateString(
                                        "en-US",
                                        {
                                            year: "numeric",
                                            month: "long",
                                            day: "numeric",
                                            hour: "numeric",
                                            minute: "numeric",
                                            second:"numeric",
                                        }
                                    )
                                }}
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
            
<script>
import axios from "axios";
import { marked } from "marked";

export default {
    name: "Home",
    data() {
        return {
            url: "",
            nick: "TWTXT User",
            avatar: "/avatar.svg",
            description: "",
            count_follow: 0,
            follow: [],
            count_posts: 0,
            posts: [],
        };
    },
    async mounted() {
        this.url = this.$route.query.url;
        await axios
            .get(`https://api.allorigins.win/get?url=${this.url}`)
            .then(async (res) => {
                const data = res.data.contents;

                const nick = data.match(/(\#\s+nick\s+=\s)(~?[A-Za-z0-9]+)/);
                this.nick = nick != null ? nick[2] : "";

                document.title = this.nick + " - TWTVK";

                const avatar = data.match(/(\#\s+avatar\s+=\s)(.+)/);
                this.avatar = avatar != null ? avatar[2] : "/avatar.svg";

                const description = data.match(/(\#\s+description\s+=\s)(.+)/);
                this.description = description != null ? description[2] : "";

                const follow = data.match(/(\#\s+follow\s+=\s)(.+)/g) || [];
                for (let i = 0; i < follow.length && i < 6; i++ ) {
                    const u = follow[i].match(/([A-Za-z]+)\s((http:|https:)+[^\s]+[\w])/);
                    if (u != null) {
                        const info = await this.getInfo(u[2]);
                        this.follow.push({
                            user: u[1],
                            avatar: info.avatar,
                            link: u[2],
                        });
                    }
                }

                this.count_follow = follow.length;

                const posts = data.match(/([0-9]+-[0-9].-[0-9].T[0-9].:[0-9].:[0-9].Z)\t(.+)/g) || [];

                posts.forEach((item) => {
                    const date = item.match(/([0-9]+-[0-9].-[0-9].T[0-9].:[0-9].:[0-9].Z)\t(.+)/)[1];
                    let text = item.match(/([0-9]+-[0-9].-[0-9].T[0-9].:[0-9].:[0-9].Z)\t(.+)/)[2];

                    const users = text.match(/@<(~?[A-Za-z0-9_]+)\s((http:|https:|gemini:)+[^\s]+[\w])>/g) || [];
                    users.forEach((item) => {
                        let u = item.match(/@<(~?[A-Za-z0-9_]+)\s((http:|https:|gemini:)+[^\s]+[\w])>/);
                        text = text.replace(u[0], `<a class="profile-right-post-user" href="/?url=${u[2]}">@${u[1]}</a>`);
                    });

                    const threads = text.match(/#<([A-Za-z0-9]+)\s((http:|https:)+[^\s]+[\w])>/g) || [];
                    threads.forEach((item) => {
                        let h = item.match(/#<([A-Za-z0-9]+)\s((http:|https:)+[^\s]+[\w])>/);
                        text = text.replace(h[0], `<a href="${h[2]}">#${h[1]}</a>`);
                    });

                    text = marked.parse(text);

                    this.posts.push({
                        date,
                        text,
                    });
                });

                this.posts = this.posts.reverse();
                this.count_posts = this.posts.length;
            })
            .catch((err) => {
                console.error(err);
            });
    },
    methods: {
        async getInfo(link) {
            let nick = "";
            let avatar = "/avatar.svg";
            let description = "";

            await axios
                .get(`https://api.allorigins.win/get?url=${link}`)
                .then((res) => {
                    const data = res.data.contents;

                    nick = data.match(/(\#\s+nick\s+=\s)(~?[A-Za-z0-9]+)/);
                    avatar = data.match(/(\#\s+avatar\s+=\s)(.+)/);
                    description = data.match(/(\#\s+description\s+=\s)(.+)/);
                })
                .catch((err) => {
                    console.error(err);
                });

            return {
                nick: nick != null ? nick[2] : "",
                avatar: avatar != null ? avatar[2] : "/avatar.svg",
                description: description != null ? description[2] : "",
            }
        }
    },
};
</script>

<style>
.profile-name {
    background-color: #eee5b8;
    padding: 4px 10px 4px;
    font-size: 11px;
    font-weight: 700;
}

.profile {
    padding: 10px;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
}

.profile-left {
    margin-right: 10px;
    min-width: calc(200px - 10px);
}

.profile-left > img {
    width: 100%;
}

.profile-left-following {
    margin-top: 10px;
}

.profile-left-following-count {
    background: #eee;
    color: #777;
    height: 14px;
    padding: 3px 8px;
    font-size: 11px;
    font-weight: normal;
    text-align: left;
}

.profile-left-follows {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    padding: 5px;
}

.profile-left-follow {
    display: flex;
    flex-direction: column;
    width: 50px;
    margin: 5px;
}

.profile-left-follow, .profile-left-follow:hover, .profile-left-follow:active {
    text-decoration: none;
}

.profile-left-follow > p {
    color: black;
    text-align: center;
    font-size: 11px;
    font-weight: normal;
    width: 100%;
    word-break: break-all;
}

.profile-left-follow:hover > p {
    text-decoration: underline;
}

.profile-left-follow > img {
    width: 100%;
}

.profile-right {
    min-width: calc(432px - 20px);
}

.profile-right-info {
    padding: 0px 6px 15px;
}

.profile-right-name {
    font-size: 13px;
    border-bottom: 1px solid #ecebe2;
    margin-top: 0;
}

.profile-right-about td {
    font-size: 11px;
}

.profile-right-about td:first-child {
    font-weight: normal;
    color: gray;
    width: 30%;
    vertical-align: top;
}

.profile-left-following-name, .profile-right-wall {
    background: #eae8cb;
    border-top: 1px solid #dfdabc;
    height: 14px;
    padding: 3px 8px;
    font-size: 11px;
    font-weight: 700;
    text-align: left;
}

.profile-right-wall > span {
    font-weight: normal;
    color: #abaf72;
}

.profile-right-post {
    display: flex;
    flex-direction: row;
    margin-top: 10px;
    border-bottom: 1px solid #ecebe2;
}

.profile-right-post img {
    width: 50px;
    height: 100%;
    margin-right: 10px;
}

.profile-right-post > div {
    width: calc(100% - 65px);
}

.profile-right-post p {
    font-size: 11px;
}

.profile-right-post > div img {
    width: 100%;
}

.profile-right-post > div a {
    color: black;
    text-decoration: black;
}

.profile-right-post > div a:hover {
    text-decoration: underline;
}

.profile-right-post-user {
    font-weight: 700;
}

.profile-right-post > div > p:nth-child(1) {
    font-weight: 700;
    margin-top: 0;
}

.profile-right-post > div > p:nth-child(2) {
    word-break: break-word;
}

.profile-right-post > div > p:nth-child(3) {
    color: gray;
}
</style>
