1. Add the `Custom Event Hook` of `Zepto Product Personalizer` in `assets/cart.js`:
```js
//! ZEPTO PRODUCT PERSONALIZER
// Listen for the Zepto Product Personalizer completion event
window.addEventListener('pplrAddToCartCompleted', function(event) {
  console.log('Zepto Product Personalizer add to cart completed - triggering cart rerender');
  
  // Fetch updated cart sections and dispatch your existing cart:rerender event
  fetch(`/?sections=cart-drawer,cart-icon`)
    .then(response => response.json())
    .then(sections => {
      // Dispatch your existing cart:rerender event
      // This will both update the cart content AND open the drawer
      document.documentElement.dispatchEvent(
        new CustomEvent("cart:rerender", {
          detail: { sections: sections },
          bubbles: true,
        })
      );
    })
    .catch(error => {
      console.error("Error fetching cart sections after Zepto completion:", error);
    });
});
```
2. Create a custom web component `Zepto Product Personalizer Preview Modal` in `assets/zepto-preview-modal.js`:
```js
class ZeptoPreviewModal extends HTMLElement {
  constructor() {
    super();
    this.modalOverlay = null;
    this.isImageUrl = this.isImageUrl.bind(this);
    this.openModal = this.openModal.bind(this);
    this.closeModal = this.closeModal.bind(this);
    this.handleKeydown = this.handleKeydown.bind(this);
  }

  connectedCallback() {
    this.init();
  }

  disconnectedCallback() {
    document.removeEventListener("keydown", this.handleKeydown);
    if (this.modalOverlay) {
      this.modalOverlay.remove();
    }
  }

  init() {
    const previewLinks = this.querySelectorAll(".zepto-preview-link");

    previewLinks.forEach((link) => {
      const href = link.getAttribute("href");

      if (this.isImageUrl(href)) {
        link.addEventListener("click", (e) => {
          e.preventDefault();
          this.openModal(href, link.textContent || "Preview");
        });

        link.style.cursor = "pointer";
        link.setAttribute("data-preview-type", "image");
      }
    });

    this.createModalOverlay();
    document.addEventListener("keydown", this.handleKeydown);
  }

  isImageUrl(url) {
    if (!url) return false;

    const imageExtensions = /\.(jpg|jpeg|png|gif|bmp|webp|svg)$/i;

    return (
      imageExtensions.test(url) ||
      url.includes("cdn.shopify.com") ||
      url.includes("/files/") ||
      url.includes("image") ||
      url.includes("img")
    );
  }

  createModalOverlay() {
    this.modalOverlay = document.createElement("div");
    this.modalOverlay.className = "zepto-modal-overlay";

    this.modalOverlay.innerHTML = `
      <div class="zepto-modal">
        <button class="zepto-modal-close" aria-label="Close preview">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <line x1="18" y1="6" x2="6" y2="18"></line>
            <line x1="6" y1="6" x2="18" y2="18"></line>
          </svg>
        </button>
        <img class="zepto-modal-image" alt="Preview" />
        <div class="zepto-modal-caption"></div>
      </div>
    `;

    document.body.appendChild(this.modalOverlay);

    const closeBtn = this.modalOverlay.querySelector(".zepto-modal-close");
    closeBtn.addEventListener("click", this.closeModal);

    this.modalOverlay.addEventListener("click", (e) => {
      if (e.target === this.modalOverlay) {
        this.closeModal();
      }
    });
  }

  openModal(imageUrl, caption = "") {
    const modalImage = this.modalOverlay.querySelector(".zepto-modal-image");
    const modalCaption = this.modalOverlay.querySelector(
      ".zepto-modal-caption"
    );

    modalImage.src = imageUrl;
    modalImage.alt = caption;
    modalCaption.textContent = caption;

    this.modalOverlay.classList.add("open");
    document.body.style.overflow = "hidden";

    modalImage.onerror = () => {
      modalCaption.textContent = "Failed to load image";
      modalImage.alt = "Failed to load image";
    };
  }

  closeModal() {
    this.modalOverlay.classList.remove("open");
    document.body.style.overflow = "";

    setTimeout(() => {
      const modalImage = this.modalOverlay.querySelector(".zepto-modal-image");
      modalImage.src = "";
    }, 200);
  }

  handleKeydown(e) {
    if (e.key === "Escape" && this.modalOverlay.classList.contains("open")) {
      this.closeModal();
    }
  }
}

customElements.define("zepto-preview-modal", ZeptoPreviewModal);
```
3. Create a css for `Zepto Preview Modal` custom web component in `assets/zepto-preview-modal.css`:
```css
/* ---------- Zepto preview modal (lightbox) ---------- */
.zepto-modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  display: none;
  align-items: center;
  justify-content: center;
  z-index: 1500;
  padding: 1.25rem;
  transition: opacity 180ms ease;
}

.zepto-modal-overlay.open {
  display: flex;
  opacity: 1;
}

.zepto-modal {
  max-width: 95%;
  max-height: 95%;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  border-radius: 0.5rem;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
  background: var(--color-popover-background, #fff);
  overflow: hidden;
}

.zepto-modal img {
  max-width: calc(100vw - 4rem);
  max-height: calc(100vh - 4rem);
  width: auto;
  height: auto;
  display: block;
  object-fit: contain;
}

.zepto-modal-close {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
  background: rgba(0, 0, 0, 0.6);
  color: #fff;
  border: none;
  width: 2rem;
  height: 2rem;
  border-radius: 999px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 10;
}

.zepto-modal-caption {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 0.5rem 0.75rem;
  background: linear-gradient(
    180deg,
    rgba(0, 0, 0, 0) 0%,
    rgba(0, 0, 0, 0.45) 100%
  );
  color: #fff;
  font-size: 0.875rem;
  text-align: left;
  pointer-events: none;
}

/* small responsiveness adjustments */
@media (max-width: 520px) {
  .zepto-modal img {
    max-width: calc(100vw - 2rem);
    max-height: calc(100vh - 2rem);
  }
}
```
4. Add the `Zepto CSS` inside of the `head` element above of `sheet-menu` and add the `Zepto Custom Web Component JS` inside of the `body` element above of `sheet-menu` in `layout/theme.liquid`:
```liquid
<!doctype html>
<html lang="{{ request.locale.iso_code }}">
  <head>
    <!-- EXISTING CODE -->
    <!-- ZEPTO PRODUCT PERSONALIZER -->
    {{ 'zepto-preview-modal.css' | asset_url | stylesheet_tag }}
    {{ 'sheet-menu.css' | asset_url | stylesheet_tag }}
    <!-- EXISTING CODE -->
  </head>

  <body>
    <!-- EXISTING CODE -->
    <!-- ZEPTO PRODUCT PERSONALIZER -->
    <script src="{{ 'zepto-preview-modal.js' | asset_url }}" defer="defer"></script>
    <script src="{{ 'sheet-menu.js' | asset_url }}" defer="defer"></script>
    <!-- EXISTING CODE -->
  </body>
</html>
```
5. Add this `Zepto Product Peronsalizer` custom properties in `snippets/cart-item.liquid`:
```liquid
{% stylesheet %}
  /*! ZEPTO PRODUCT PERSONALIZER */
  .zepto-custom-properties.first-zepto {
    margin-top: 0.5rem; /* adjust as needed */
  }

  .zepto-custom-field {
    font-size: 0.875rem; /* 14px */
    line-height: 1.25rem; /* 20px */
  }

  .zepto-preview-link {
    font-size: 0.875rem; /* 14px */
    line-height: 1.25rem; /* 20px */
    color: var(--color-accent-background);
  }
{% endstylesheet %}

<!-- ZEPTO PRODUCT PERSONALIZER -->
{% comment %} Cart Item Custom Properties (Zepto Product Personalizer) {% endcomment %}
{% comment %}
  {%- for p in item.properties -%}
   {%- assign first_char = p.first | slice: 0, 1 -%}
   {%- if first_char != '_' -%}
      <div class="zepto-custom-properties">
        {%- if p.last contains 'http' -%}
          <a target="_blank" href="{{ p.last }}" src="{{ p.last }}" download class="zepto-preview-link">Preview</a>
        {%- else -%}
          <span class="zepto-custom-field">{{ p.last }}</span>
        {%- endif -%}
      </div>
    {%- endif -%}
  {%- endfor -%}
{% endcomment %}

{%- assign first_output = false -%}
{%- for p in item.properties -%}
  {%- assign first_char = p.first | slice: 0, 1 -%}
  {%- if first_char != '_' -%}
    <div class="zepto-custom-properties {% if first_output == false %}first-zepto{% endif %}">
      {%- if p.last contains 'http' -%}
        <zepto-preview-modal>
          <a
            href="{{ p.last }}"
            rel="noopener noreferrer"
            target="_blank"
            data-preview-url="{{ p.last }}"
            class="zepto-preview-link"
          >
            Preview
          </a>
        </zepto-preview-modal>
      {%- else -%}
        <span class="zepto-custom-field">{{ p.last }}</span>
      {%- endif -%}
    </div>

    {%- if first_output == false -%}
      {%- assign first_output = true -%}
    {%- endif -%}
  {%- endif -%}
{%- endfor -%}
``` 
