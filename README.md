
/**
 * Days of the Apocalypse - 3D Background Engine
 * Creates an interactive 3D parallax scene with abandoned buildings on fire.
 */
class ApocalypseEngine {
    constructor() {
        this.world = document.querySelector('.apocalypse-world');
        this.stage = document.querySelector('.bg-3d-stage');
        
        // Configuration settings
        this.config = {
            buildingCount: { distant: 6, midground: 8 },
            parallaxStrength: 0.04, // Mouse tilt intensity
            smoothing: 0.1         // Camera lerp speed
        };

        // Camera Tracking State
        this.camera = { x: 0, y: 0, targetX: 0, targetY: 0 };

        this.init();
    }

    init() {
        // Build the procedural 3D elements dynamically
        this.generateLayer('.layer-distant', this.config.buildingCount.distant);
        this.generateLayer('.layer-midground', this.config.buildingCount.midground);
        
        // Bind mouse movement for interactive 3D parallax shifting
        window.addEventListener('mousemove', (e) => this.handleMouseMove(e));
        
        // Start the continuous rendering animation loop
        this.animate();
    }

    /**
     * Generates HTML layout for apocalyptic buildings, windows, and fires programmatically
     */
    generateLayer(selector, count) {
        const layer = this.stage.querySelector(selector);
        if (!layer) return;

        const cluster = document.createElement('div');
        cluster.className = 'building-cluster';

        const structuralStyles = ['skyscraper', 'tower', 'block'];

        for (let i = 0; i < count; i++) {
            const building = document.createElement('div');
            const style = structuralStyles[Math.floor(Math.random() * structuralStyles.length)];
            building.className = `building ${style}`;
            
            // Randomize building dimensions slightly for organic silhouettes
            building.style.width = `${100 + Math.random() * 90}px`;
            building.style.height = `${45 + Math.random() * 45}%`;

            // Spatially offset depth positioning along the CSS 3D Z-axis
            const zOffset = Math.random() * 40 - 20; 
            building.style.transform = `translateZ(${zOffset}px)`;

            // Inject devastating roof-top inferno blazes
            if (Math.random() > 0.3) {
                building.appendChild(this.createFireBlaze());
            }

            // Populate window matrices
            building.appendChild(this.generateWindowGrid());
            cluster.appendChild(building);
        }

        layer.appendChild(cluster);
    }

    /**
     * Spawns randomized structural matrices of shattered or burning windows
     */
    generateWindowGrid() {
        const grid = document.createElement('div');
        grid.className = 'window-grid';
        
        // Layout grid variation based on random density blueprints
        grid.style.gridTemplateColumns = `repeat(${Math.floor(Math.random() * 2) + 3}, 1fr)`;

        const windowCount = Math.floor(Math.random() * 12) + 6;
        for (let i = 0; i < windowCount; i++) {
            const win = document.createElement('div');
            const roll = Math.random();

            if (roll < 0.35) {
                win.className = 'window inferno-inside';
                // De-synchronize individual fire frame flickers via randomized styling
                win.style.animationDelay = `${Math.random() * -0.5}s`;
            } else if (roll < 0.75) {
                win.className = 'window shattered';
            } else {
                win.className = 'window'; // Completely dark, burnt shell
            }

            grid.appendChild(win);
        }

        return grid;
    }

    /**
     * Constructs the fire elemental nodes matching the CSS keyframes
     */
    createFireBlaze() {
        const container = document.createElement('div');
        container.className = 'blaze-container';
        container.style.left = `${Math.random() * 20}%`;

        const tongueCount = Math.floor(Math.random() * 3) + 2;
        for (let i = 0; i < tongueCount; i++) {
            const tongue = document.createElement('div');
            tongue.className = 'flame-tongue';
            tongue.style.animationDelay = `${Math.random() * -0.8}s`;
            tongue.style.animationDuration = `${0.3 + Math.random() * 0.5}s`;
            container.appendChild(tongue);
        }

        return container;
    }

    /**
     * Formulates targeting updates based on normalized peripheral screen coordinates
     */
    handleMouseMove(e) {
        const normX = (e.clientX / window.innerWidth) - 0.5;
        const normY = (e.clientY / window.innerHeight) - 0.5;

        this.camera.targetX = normX * this.config.parallaxStrength * 300; 
        this.camera.targetY = normY * this.config.parallaxStrength * -150; 
    }

    /**
     * Executes the Core Engine Frame Update Loops utilizing Linear Interpolation (Lerp)
     */
    animate() {
        // Interpolate camera positioning to deliver glassy, weighted cinematic pans
        this.camera.x += (this.camera.targetX - this.camera.x) * this.config.smoothing;
        this.camera.y += (this.camera.targetY - this.camera.y) * this.config.smoothing;

        // Apply physical transformation directly to the 3D CSS viewport element
        if (this.stage) {
            this.stage.style.transform = `
                rotateY(${this.camera.x}deg) 
                rotateX(${this.camera.y}deg)
            `;
        }

        requestAnimationFrame(() => this.animate());
    }
}

// Instantiate the post-apocalyptic environment engine once elements are accessible
document.addEventListener('DOMContentLoaded', () => {
    window.apocalypseScene = new ApocalypseEngine();
});

