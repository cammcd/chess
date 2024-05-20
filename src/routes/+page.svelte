<script lang="ts">
    import { onMount } from "svelte";
    import { writable } from "svelte/store";

    let usernameInput: string = '';
    let username: string = '';
    let country: string = '';
    let followers: number = 0;
    let is_streamer: boolean = false;
    let league: string = '';
    let url: string = '';
    let best: number = 0;
    let current: number = 0;

    const fetchPlayerData = async () => {
        try {
            const response = await fetch(`https://api.chess.com/pub/player/${usernameInput}`);
            if (!response.ok) {
                throw new Error('Player not found');
            }
            const data = await response.json();
            username = data.username;
            country = data.country;
            followers = data.followers;
            is_streamer = data.is_streamer;
            league = data.league;
            url = data.url;
        } catch (error) {
            console.error(error);
        }
    };
    const fetchPlayerStats = async () => {
        try {
            const response = await fetch(`https://api.chess.com/pub/player/${usernameInput}/stats`);
            if (!response.ok) {
                throw new Error('Player not found');
            }
            const data = await response.json();
            best = data.chess_rapid?.best?.rating || null;
            current = data.chess_rapid?.last?.rating || null;
        } catch (error) {
            console.error(error);
        }
    };

    const getData = async () => {
        fetchPlayerData()
        fetchPlayerStats()
    }
</script>

<main>
    <h1>Chess Player Info</h1>
    <input type="text" bind:value={usernameInput} placeholder="Enter username" />
    <button on:click={getData}>Get Player Info</button>

    {#if username}
        <div>
            <p><strong>Username:</strong> {username}</p>
            <p><strong>Country:</strong> {country}</p>
            <p><strong>Followers:</strong> {followers}</p>
            <p><strong>Streamer:</strong> {is_streamer ? 'Yes' : 'No'}</p>
            <p><strong>League:</strong> {league}</p>
            <p><strong>Profile:</strong> <a href={url} target="_blank">{url}</a></p>
            <p><strong>Best Rating:</strong> {best !== null ? best : 'No data to display'}</p>
            <p><strong>Current Rating:</strong> {current !== null ? current : 'No data to display'}</p>
        </div>
    {/if}
</main>

<style>
    main {
        padding: 1em;
        font-family: Arial, sans-serif;
    }
    input {
        margin-right: 0.5em;
        padding: 0.5em;
    }
    button {
        padding: 0.5em;
    }
    div {
        margin-top: 1em;
        padding: 1em;
        border: 1px solid #ccc;
    }
</style>
