<script context="module">
  export async function preload(page) {
    const { params } = page;
    const res = await this.fetch('http://localhost:8080/meetups/'+ params.id)
    const meetup = await res.json();
    return { loadedMeetup: meetup}
  }
</script>

<script>
import { onDestroy, createEventDispatcher } from 'svelte';
import meetupItems from '../meetups-store.js';
import Button from '../components/UI/Button.svelte';

export let loadedMeetup;

const dispatch = createEventDispatcher();

</script>

<style>
section {
  margin-top: 4rem;
}

.image {
  width: 100%;
  height: 25rem;
}

img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.image {
  background: #e7e7e7;
}

.content {
  text-align: center;
  width: 80%;
  margin: auto;
}

h1 {
  font-size: 2rem;
  font-family: 'Roboto Slab', sans-serif;
  margin: 0.5rem 0;
}

h2 {
  font-size: 1.25rem;
  color: #6b6b6b;
}

p {
  font-size: 1.5rem;
}
</style>

<section>
    <div class="image">
        <img src="{loadedMeetup.imageURL}" alt="{loadedMeetup.title}">
    </div>
    <div class="content">
        <h1>{loadedMeetup.title}</h1>
        <h2>{loadedMeetup.subtitle} - {loadedMeetup.address}</h2>
        <p>{loadedMeetup.description}</p>
        <Button href="mailto:{loadedMeetup.contactEmail}">contact</Button>
        <Button 
            type="button"
            mode="outline"
            href={'/'}>close</Button>
    </div>
</section>