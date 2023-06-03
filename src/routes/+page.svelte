<script lang="ts">
    import axios from "axios";
    import { onMount, onDestroy, afterUpdate, beforeUpdate } from "svelte";
    import { fade, fly } from "svelte/transition";

    let searchString="";
    let bSearching = false;
    let photos: {
        id: string;
        alt_description: string;
        urls: { regular: string };
        }[] = [];

    const URL ="https://api.unsplash.com/search/photos?page=1&query="
    const API_KEY="gwDfZRd4gwb3HXc11ncooqKDqa2SZ-8T_1XINbg0ETo"

    const fetchData = async () => {
        const response = await axios.get(
            `${URL}${searchString}&client_id=${API_KEY}`
        )
        // console.log(response.data.results)
        photos = response.data.results;
        console.log(photos)
    }

    onMount(() => {
        //fetchData();
    })

    onDestroy(() => {
        console.log("goodbye there");
    });

    beforeUpdate(() => {
        console.log("I ran before the app updated");
    });

    beforeUpdate(() => {
        console.log("I ran after the app updated");
    });

    const handleSearch = () => {
        console.log("handleSearch");
        if(searchString==="") return;

        photos = [];
        fetchData();
        bSearching=true;
        searchString="";
    }
</script>

<main>
    <div class="container">
        <div class="header">
            <h1>Image Gallery</h1>
            <div class="input-container">
                <input type="text" class="input" bind:value={searchString}>
                <button class="button" on:click={handleSearch}>Search</button>
            </div>
        </div>
    </div>
    <div class="photos">
        {#each photos as photo, i (photo.id)}
            <img 
            src={photo.urls.regular} 
            alt={photo.alt_description} class="image"
            in:fly={{ y: 200, duration: 2000, delay: i * 200 }}
            out:fade
            >
        {:else}
            {#if bSearching}
                <h2>Loading...</h2>
            {:else}
                <p class="hint">Enter search string and then click Search button.</p>
            {/if}
            
        {/each}
    </div>
</main>

<style>
    .image {
      width: 400px;
      height: 250px;
      margin: 5px;
    }
    .photos {
      display: flex;
      justify-content: center;
      text-align: center;
      flex-wrap: wrap;
    }
    .container {
      width: 1230px;
      margin: 0 auto;
      justify-content: center;
    }
    .header {
      text-align: center;
      font-size: 20px;
    }
    .input {
      padding: 10px;
      width: 400px;
      border-radius: 10px;
      outline: none;
      border: 1px solid gray;
      font-size: 20px;
    }
    .button {
      padding: 10px;
      font-size: 20px;
      background-color:darkcyan;
      border-radius: 10px;
      border: none;
      color: white;
    }
    .input-container {
      margin-bottom: 40px;
    }
    .hint{
        color: gray;
        font-size: 12px;
        font-style: italic;
    }
  </style>