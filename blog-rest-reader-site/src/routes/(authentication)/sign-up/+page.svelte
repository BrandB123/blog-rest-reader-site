<script lang="ts">
    import { goto } from '$app/navigation';
  
      let firstName = $state("");
      let lastName = $state("");
      let name = $derived(`${firstName} ${lastName}`);
      let email = $state("");
      let password = $state("");
  
      async function addUser(name: string, email: string, password: string){
          try {
              const response = await fetch(`${import.meta.env.VITE_API_URL}api/signup`, {
                  method: "POST",
                  body: JSON.stringify({
                      name,
                      email: email.toLowerCase(), 
                      password, 
                      authorStatus: false
                  }),
                  headers: {
                      "Content-Type": "application/json"
                  }
              });
              
              if (response.status === 422){ 
                  alert("Name, Email, and Password must be provided")
              }
  
              if (response.status === 409){
                  alert("A user with this email already exists.")
              }
              
              if (!response.ok){
                  throw new Error(`Response Status: ${response.status}`);
              }
  
              goto('/log-in')
  
          } catch(err) {
              console.error(err);
  
              if ((err as Error).name === 'TypeError'){
                  alert("Service currently unavailable. Sorry for the inconvenience. Please try again later.")
              }
          }
      }
  </script>
  
  <h1 class="text-3xl pl-4">Create an Account</h1>
  <form action="/"
      class="bg-neutral-100 w-3/4 md:w-1/2 lg:w-1/3 max-w-lg my-3 mx-auto p-5 rounded-lg border border-neutral-500">
  
      <label for="first-name">First Name:</label>
      <input 
          type="text"
          id = "first-name"
          required 
          bind:value={firstName} 
          class="block pl-2 mx-auto mb-2 w-4/5 border border-neutral-300 rounded-md">
  
      <label for="last-name">Last Name:</label>
      <input 
          type="text"
          id = "last-name"
          required 
          bind:value={lastName} 
          class="block pl-2 mx-auto mb-2 w-4/5 border border-neutral-300 rounded-md">
  
      <label for="email">Email:</label>
      <input 
          type="text"
          id = "email"
          required 
          bind:value={email} 
          class="block pl-2 mx-auto mb-2 w-4/5 border border-neutral-300 rounded-md">
  
      <label for="password">Password:</label>
      <input 
          type="password"
          id = "password"
          required 
          minlength="8"
          bind:value={password} 
          class="block pl-2 mx-auto mb-2 w-4/5 border border-neutral-300 rounded-md">
  
      <button 
          type="submit"
          class="p-1 mt-4 w-2/3 lg:w-1/2 block mx-auto border rounded-lg border-blue-600 bg-blue-500 text-neutral-200"
          onclick={(event) => {
              event.preventDefault();
              if (!/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/.test(email)){
                  alert("Invalid email entry.")
              } else if (!/^(\S+)(\s*)(\S+)$/.test(name)){
                  alert("Invalid name entry. First and last name required")
              } else {
                  addUser(name, email, password)
              }
          }}>
          Create Account
      </button>
  </form>