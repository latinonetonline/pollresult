<script>
  import { onMount, afterUpdate, beforeUpdate } from "svelte";
  import Chart from "chart.js";
  import { config } from "./config.js";
  import Loading from "./Loading.svelte";

  let isLoading = true;
  let pollResult;
  let myChart;

  const fetchPollResult = () => {
    let pollId = window.location.hash.replace('#', '');
    fetch(
      `${config.api}/api/Polls/GetResult/${pollId}`
    )
      .then(res => res.json())
      .then(data => {
        pollResult = data;
        isLoading = false;
      });
  };

  const updatePollResult = () => {
    isLoading = true;
    fetchPollResult();
  };

  onMount(() => {
    fetchPollResult();
  });

  afterUpdate(() => {
    if (!isLoading) {
      let configChart = {
        type: "pie",
        data: {
          datasets: [
            {
              data: [
                pollResult.options[0].votes,
                pollResult.options[1].votes,
                pollResult.options[2].votes,
                pollResult.options[3].votes
              ],
              backgroundColor: [
                "rgb(255, 99, 132)",
                "rgb(255, 159, 64)",
                "rgb(75, 192, 192)",
                "rgb(54, 162, 235)"
			  ],
              label: "Dataset 1"
            }
          ],
          labels: [
            pollResult.options[0].text,
            pollResult.options[1].text,
            pollResult.options[2].text,
            pollResult.options[3].text
          ]
        },
        options: {
          responsive: true
        }
      };
      let ctx = document.getElementById("chart-area").getContext("2d");
      myChart = new Chart(ctx, configChart);
    }
  });
</script>

{#if isLoading}
  <Loading />
{:else}
  <div id="canvas-holder">
    <canvas id="chart-area" />
  </div>
  <button on:click={updatePollResult}>Actualizar</button>
{/if}
