<script>
  export let imageUrl;
</script>

<div class="card">
  <img src={imageUrl} alt="Card image" />
</div>

<style>
  .card img {
    width: 100%;
    height: auto;
    object-fit: cover;
  }
</style>
