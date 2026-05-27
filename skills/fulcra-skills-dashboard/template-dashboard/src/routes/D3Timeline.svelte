<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  // The raw data points to visualize on the timeline
  let { data = [], title = "Activity Timeline", color = "#4a5568" } = $props();

  /** @type {HTMLElement} */
  let container;
  
  /** @type {number | null} */
  let hoverIndex = $state(null);

  // Parse times
  let parsedData = $derived(data.map(d => ({
    ...d,
    date: new Date(d.time || d.date)
  })).sort((a, b) => a.date - b.date));

  let displayedDetails = $derived.by(() => {
      if (!parsedData || parsedData.length === 0) return [];
      let startIndex = hoverIndex !== null ? hoverIndex : parsedData.length - 1;
      let details = [];
      for (let i = 0; i < 5; i++) {
          let idx = startIndex - i;
          if (idx >= 0 && idx < parsedData.length) {
              details.push({ item: parsedData[idx], isHovered: hoverIndex === idx });
          }
      }
      return details;
  });

  function getIcon(d) {
      // Default icons for unthemed dashboard
      if (d.type === 'backup' || (d.label && d.label.toLowerCase().includes('backup'))) return '💾';
      if (d.type === 'annotation' || (d.label && d.label.toLowerCase().includes('note'))) return '📝';
      return '⏺️';
  }

  function getTier(d) {
      if (d.type === 'backup' || (d.label && d.label.toLowerCase().includes('backup'))) return 'Archival';
      if (d.type === 'annotation' || (d.label && d.label.toLowerCase().includes('note'))) return 'Annotation';
      return 'Event';
  }

  onMount(() => {
    if (!parsedData || parsedData.length === 0) return;

    // Dimensions
    const width = container.clientWidth;
    const height = 100;
    const margin = { top: 20, right: 30, bottom: 30, left: 30 };

    // Clear previous SVG if re-rendering
    d3.select(container).selectAll("*").remove();

    const svg = d3.select(container)
      .append("svg")
      .attr("width", width)
      .attr("height", height)
      .style("overflow", "visible");

    // Scales
    const x = d3.scaleTime()
      .domain(d3.extent(parsedData, d => d.date))
      .range([margin.left, width - margin.right]);

    // Axis
    const xAxis = d3.axisBottom(x)
      .ticks(5)
      .tickFormat(d3.timeFormat("%b %d, %H:%M"));

    svg.append("g")
      .attr("transform", `translate(0,${height - margin.bottom})`)
      .call(xAxis)
      .attr("color", "#a0aec0")
      .selectAll("text")
      .style("font-family", "inherit")
      .style("fill", "#718096");

    // Timeline Line
    svg.append("line")
      .attr("x1", margin.left)
      .attr("y1", (height - margin.bottom) / 2)
      .attr("x2", width - margin.right)
      .attr("y2", (height - margin.bottom) / 2)
      .attr("stroke", "#e2e8f0")
      .attr("stroke-width", 2);

    // Data Points
    svg.selectAll(".dot")
      .data(parsedData)
      .enter()
      .append("circle")
      .attr("class", "dot")
      .attr("cx", d => x(d.date))
      .attr("cy", (height - margin.bottom) / 2)
      .attr("r", 6)
      .attr("fill", color)
      .attr("stroke", "#fff")
      .attr("stroke-width", 2)
      .style("cursor", "pointer")
      .on("mouseover", (event, d) => {
        d3.select(event.currentTarget)
          .transition()
          .duration(100)
          .attr("r", 10);
        
        // Find index
        hoverIndex = parsedData.findIndex(item => item === d);
      })
      .on("mouseout", (event) => {
        d3.select(event.currentTarget)
          .transition()
          .duration(200)
          .attr("r", 6);

        hoverIndex = null;
      });
  });
</script>

<div class="timeline-wrapper">
  <h4>{title}</h4>
  <div class="chart-container" bind:this={container}></div>

  <div class="mt-4 space-y-4" style="margin-top: 1rem; display: flex; flex-direction: column; gap: 1rem;">
    {#each displayedDetails as {item: detail, isHovered}}
      <div style="display: flex; gap: 1rem; align-items: flex-start; background: #ffffff; padding: 1rem; border: 1px solid {isHovered ? color : '#e2e8f0'}; border-radius: 6px; position: relative; overflow: hidden; transition: all 0.3s; box-shadow: {isHovered ? '0 4px 6px rgba(0,0,0,0.1)' : 'none'}; transform: {isHovered ? 'scale(1.01)' : 'scale(1)'};">
        
        <div style="width: 3rem; height: 3rem; background: #f7fafc; flex-shrink: 0; display: flex; align-items: center; justify-content: center; padding: 0.375rem; border: 1px solid #edf2f7; border-radius: 50%; z-index: 10; position: relative; transition: all 0.3s;">
          <div style="font-size: 1.25rem;">
            {getIcon(detail)}
          </div>
        </div>
        
        <div style="flex: 1; z-index: 10; position: relative; transition: all 0.3s;">
          <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 0.25rem; border-bottom: 1px solid #edf2f7; padding-bottom: 0.5rem; transition: all 0.3s;">
            <h4 style="color: #2d3748; font-weight: 600; margin: 0; display: flex; align-items: center; gap: 0.5rem; font-size: 0.95rem;">
              {detail.date.toLocaleString(undefined, { weekday: 'short', month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' })}
            </h4>
            <span style="font-size: 0.65rem; font-weight: bold; text-transform: uppercase; letter-spacing: 0.05em; padding: 0.125rem 0.5rem; border-radius: 12px; background: #edf2f7; color: #4a5568; border: 1px solid #e2e8f0;">
              {getTier(detail)}
            </span>
          </div>
          <p style="color: #4a5568; font-size: 0.875rem; line-height: 1.5; margin-top: 0.5rem; margin-bottom: 0;">
            <strong style="color: #2d3748;">{detail.label || detail.type}</strong><br/>
            {detail.details || detail.size || ''}
          </p>
        </div>
      </div>
    {/each}
  </div>
</div>

<style>
  .timeline-wrapper {
    margin: 1rem 0;
  }

  h4 {
    margin: 0 0 1rem 0;
    color: #2d3748;
    font-weight: 600;
    font-size: 1.1rem;
  }

  .chart-container {
    width: 100%;
    height: 100px;
    position: relative;
  }
</style>
