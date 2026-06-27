<div>
  <div id="mastodon-timeline">loading archival stream...</div>

  <script>
    async function loadMastodon() {
      const url = 'https://mastodon.world/api/v1/accounts/115560684927429188/statuses?limit=15&exclude_replies=true&exclude_reblogs=true';
      const container = document.getElementById('mastodon-timeline');
      
      if (!container) return;

      try {
        const res = await fetch(url);
        if (!res.ok) throw new Error('Network response was not ok');
        const statuses = await res.json();

        container.innerHTML = statuses.map(status => {
          const images = status.media_attachments
            .filter(m => m.type === 'image')
            .map(m => `
              <div style="margin: 1rem 0;">
                <img src="\${m.preview_url}" alt="\${m.description || ''}" style="max-width: 100%; border-radius: 4px; border: 1px solid #e5e7eb;">
              </div>
            `).join('');

          const date = new Date(status.created_at).toLocaleDateString();

          return `
            <article style="margin-bottom: 3rem; border-bottom: 1px dashed #e5e7eb; padding-bottom: 2rem;">
              <div style="line-height: 1.6;">\${status.content}</div>
              \${images}
              <div style="margin-top: 1rem; font-size: 0.8rem;">
                <a href="\${status.url}" target="_blank" style="color: #6b7280; text-decoration: none;">
                  [ \${date} ]
                </a>
              </div>
            </article>
          `;
        }).join('');
      } catch (err) {
        container.textContent = 'unable to load feed.';
        console.error('Mastodon fetch error:', err);
      }
    }
    
    // Execute once the script is loaded
    loadMastodon();
  </script>
</div>