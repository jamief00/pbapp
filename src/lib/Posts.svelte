<script lang="ts">
    import { onMount, onDestroy } from "svelte";
    import { currentUser, pb } from "./pocketbase";

    let posts = [];

    onMount(async () => {
        const resultList = await pb.collection('posts').getList(1, 10, {
            sort: 'created',
            expand: 'user',
        });

        posts = resultList.items;

        pb
            .collection('posts')
            .subscribe('*', async ({ action, record }) => {
                if (action === 'create'){
                    const user = await pb.collection('users').getOne(record.user);
                    record.expand = { user };
                    posts = [...posts, record];
                }
            });
    });

</script>

<div class="messages">

    {#each posts as post (post.id)}
        <div class="msg">
            <img 
                class="avatar"
                src={`https://avatars.dicebear.com/api/identicon/${post.expand?.user?.username}.svg`}
                alt="avatar"
                width="30px"
            />

            <div>
                <small>
                    {post.title} ({post.subtitle}) by @{post.expand?.user?.username}
                </small>
                <p class="msg-text">{post.body}</p>
            </div>

        </div>
    {/each}

</div>