<script lang="ts">
    import { goto } from "$app/navigation";

    let username = $state("");
    let password = $state("");

    async function login(username: string, password: string){
        try {
            const response = await fetch(`${import.meta.env.VITE_API_URL}api/login`, {
                method: "POST",
                body: JSON.stringify({
                    username,
                    password
                }),
                headers: {
                    "Content-Type": "application/json"
                }
            });

            if (response.status === 401){
                alert("Invalid login credentials.")
            }

            if (!response.ok){
                throw new Error("Response Status: ", response.status)
            }

            const json = await response.json();

            localStorage.setItem("jwtAuth", json.token)
            goto('/')
            
        } catch(err){
            console.error(err);

            if (err.name === 'TypeError'){
                alert("Service currently unavailable. Sorry for the inconvenience. Please try again later.")
            }
        }
    }

</script>

<h1 class="text-3xl pl-4">Log In</h1>
<form action="/"
    class="bg-neutral-100 w-3/4 md:w-1/2 lg:w-1/3 max-w-lg my-3 mx-auto p-5 rounded-lg border border-neutral-500">

    <label 
        for="email" 
        class="mx-auto">
        Email:
    </label>
    <input 
        type="text" 
        id="email" 
        bind:value={username}
        class="block pl-2 mx-auto mb-2 w-4/5 border border-neutral-300 rounded-md">

    <label 
        for="password" 
        class="mx-auto">
        Password:
    </label>
    <input 
        type="password" 
        id="password" 
        bind:value={password}
        class="block pl-2 mx-auto mb-2 w-4/5 border border-neutral-300 rounded-md">

        <button
            type="submit"
            class="p-1 mt-4 w-1/2 lg:w-1/4 block mx-auto border rounded-lg border-blue-600 bg-blue-500 text-neutral-200"
            onclick={(event) => {
                event.preventDefault();
                login(username, password);
            }}>
            Log in
        </button>
</form>