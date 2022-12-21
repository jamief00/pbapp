<script lang="ts">
    import { currentUser, pb } from "./pocketbase";

    let username: string;
    let password: string;

    async function login() {
        await pb.collection('users').authWithPassword(username, password);
        
    }

    async function register() {
        try {
            const data = {
                username,
                password,
                passwordConfirm: password,
                location: 'earth',
            };

            const createdUser = await pb.collection('users').create(data);
            await login();
        } catch (err) {
            console.log(err);
        }
    }

    async function logout() {
        pb.authStore.clear();
    }

</script>

{#if $currentUser}
  <p>Welcome, {$currentUser.username}</p>

  <button on:click={logout}>Logout</button>
{:else}
  <form on:submit|preventDefault>
    <input placeholder="username" type="text" bind:value={username} />

    <input placeholder="password" type="password" bind:value={password} />

    <button on:click={register}>Register</button>
    <button minlength="8" on:click={login}>Login</button>
  </form>
{/if}
