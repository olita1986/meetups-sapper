<script context="module">
    export async function preload(page) {
      const res = await this.fetch('http://localhost:8080/meetups');
      console.log(res);
      const meetups = await res.json()
      console.log(meetups);
      return {fetchedMeetups : meetups.reverse()}
        // return this.fetch('http://localhost:8080/meetups')
        //   .then(res => {
        //       if (!res.ok) {
        //           throw new Error('An error has ocurred');
        //       }
        //       return res.json()
        //   })
        //   .then(meetups => {
        //       console.log(meetups);
        //       //meetupItems.setMeetups(meetups.reverse());
        //       return {fetchedMeetups : meetups};
        //   })
        //   .catch(err => {
        //       console.log(err);
        //       this.error(500,'could not fetch meetups');
        //   })
    }
</script>

<script>
import meetupItems from '../meetups-store.js'
import {onMount, onDestroy} from 'svelte';
import MeetupItem from '../components/Meetup/MeetupItem.svelte';
import MeetupFilter from '../components/Meetup/MeetupFilter.svelte';
import { scale } from 'svelte/transition';
import { flip } from 'svelte/animate';
import Button from '../components/UI/Button.svelte';
import EditMeetup from '../components/Meetup/EditMeetup.svelte';

export let fetchedMeetups;
let loadedMeetups = [];
let editedId;
let editMode;

let unsubscribe;

let showFavMeetups = false

$: filteredMeetups = showFavMeetups ? loadedMeetups.filter( m => m.isFavorite) : loadedMeetups;

onMount(() => {
  unsubscribe = meetupItems.subscribe( items => {
    loadedMeetups = items;
  })
  meetupItems.setMeetups(fetchedMeetups);
})

onDestroy(() => {
  if (unsubscribe) {
    unsubscribe();
  }
})
const setFilter = (e) => {
  showFavMeetups = e.detail === 1;
}

const addMeetup = () => {
    editMode = null;
    editedId = null;
}

const editMeetup = (e) => {
    editedId = e.detail;
    editMode = 'edit';
}
</script>

<style>
section {
  width: 100%;
  display: grid;
  grid-template-columns: 1fr;
  grid-gap: 1rem;
}

@media (min-width: 768px) {
  section {
    grid-template-columns: repeat(2, 1fr);
  }
}

#meetups-control {
  margin: 1rem;
}
</style>

<svelte:head>
	<title>Sapper project template</title>
</svelte:head>

<div class="meetup-control">
        <Button 
            on:click={() => editMode = 'edit'}
        >New Meetup</Button>
    </div>
    {#if editMode === 'edit'}
        <EditMeetup _id={editedId} on:addmeetup={addMeetup} on:close={() => {
            editMode = null;
            editedId = null;
            }}/>
    {/if}
<section id="meetups-control">
  <MeetupFilter on:select={setFilter}/>
</section>
<section id="meetups">
    {#each filteredMeetups as meetup (meetup._id)}
      <div transition:scale animate:flip={{duration:300}}>
        <MeetupItem {...meetup} on:showdetails on:editmeetup={editMeetup}/>
      </div>
    {/each}
</section>