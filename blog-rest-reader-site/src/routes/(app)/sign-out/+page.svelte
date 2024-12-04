<script>
    import { goto } from '$app/navigation';

    const signedOut = new Promise((resolve, reject) => {
        setTimeout(() => {
            if (localStorage.getItem("jwtAuth")){
                localStorage.removeItem("jwtAuth");
            }
            resolve('successfully');
            // return reject('failure');
            setTimeout(() => {goto('/log-in')}, 1000);
        }, 1500);
    });

</script>

{#await signedOut}
    <p class="text-center mt-8">
        Wait a moment while you are signed out...
    </p>
{:then signedOut}
    <p class="text-center mt-8">
        You have been signed out {signedOut}
    </p>
{:catch error}
    <p class="text-center mt-8">
        Error signing out
    </p>
{/await}