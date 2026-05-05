# Radon_Estimator_Dr.Mutlu_Zeybek

import matplotlib.pyplot as plt
import matplotlib.patches as patches
from matplotlib.patches import FancyBboxPatch, FancyArrowPatch, Circle, Rectangle
import numpy as np


def draw_workflow_diagram():
    """Draw a simple workflow diagram for indoor radon concentration estimation"""

    fig, ax = plt.subplots(1, 1, figsize=(12, 10))
    ax.set_xlim(0, 10)
    ax.set_ylim(0, 12)
    ax.axis("off")

    # Define box positions (x, y) - center coordinates
    boxes = {
        "start": (5, 11.0),
        "input": (5, 9.5),
        "calc_Q0": (5, 8.0),
        "calc_Q1": (5, 6.5),
        "calc_Q2": (5, 5.0),
        "porosity": (5, 3.5),
        "risk": (5, 2.0),
        "end": (5, 0.5),
    }

    # Define box dimensions
    width = 6.0
    height = 0.8

    # Function to draw rounded rectangle
    def draw_rounded_rect(x, y, w, h, color, text, fontsize=10, text_color="black"):
        rect = FancyBboxPatch(
            (x - w / 2, y - h / 2),
            w,
            h,
            boxstyle="round,pad=0.1",
            facecolor=color,
            edgecolor="navy",
            linewidth=2,
        )
        ax.add_patch(rect)
        ax.text(
            x,
            y,
            text,
            fontsize=fontsize,
            ha="center",
            va="center",
            color=text_color,
            weight="bold",
        )

    # Function to draw diamond (parallelogram) for decision
    def draw_diamond(x, y, w, h, color, text, fontsize=9):
        # Create diamond shape (rotated square)
        diamond = patches.Polygon(
            [[x, y + h / 2], [x + w / 2, y], [x, y - h / 2], [x - w / 2, y]],
            closed=True,
            facecolor=color,
            edgecolor="darkgreen",
            linewidth=2,
        )
        ax.add_patch(diamond)
        ax.text(x, y, text, fontsize=fontsize, ha="center", va="center", weight="bold")

    # Function to draw ellipse/circle for terminal
    def draw_terminal(x, y, w, h, color, text, fontsize=12):
        ellipse = patches.Ellipse(
            (x, y), w, h, facecolor=color, edgecolor="black", linewidth=2
        )
        ax.add_patch(ellipse)
        ax.text(x, y, text, fontsize=fontsize, ha="center", va="center", weight="bold")

    # Draw boxes
    # Start (terminal)
    draw_terminal(
        boxes["start"][0], boxes["start"][1], 1.5, 0.6, "lightgray", "START", 12
    )

    # Input parameters (rectangle)
    input_text = "INPUT PARAMETERS\nQl (lithology) | Qf (faults) | Qb (materials)"
    draw_rounded_rect(
        boxes["input"][0], boxes["input"][1], width, height, "lightblue", input_text, 9
    )

    # Calculate Q0
    draw_rounded_rect(
        boxes["calc_Q0"][0],
        boxes["calc_Q0"][1],
        width,
        height,
        "lightgreen",
        "STEP 1: Q₀ = Ql + Qf (Ground surface)",
        10,
    )

    # Calculate Q1
    draw_rounded_rect(
        boxes["calc_Q1"][0],
        boxes["calc_Q1"][1],
        width,
        height,
        "lightgreen",
        "STEP 2: Q₁ = Ql + Qf + Qb (Ground floor)",
        10,
    )

    # Calculate Q2
    draw_rounded_rect(
        boxes["calc_Q2"][0],
        boxes["calc_Q2"][1],
        width,
        height,
        "lightgreen",
        "STEP 3: Q₂ = Qb (First floor + above)",
        10,
    )

    # Porosity weighting (diamond/decision)
    porosity_text = (
        "STEP 4: POROSITY WEIGHTING\nn₀=1 (lithology) | n₁=1 (slab) | n₂=0, n₄=0"
    )
    draw_diamond(
        boxes["porosity"][0],
        boxes["porosity"][1],
        5.5,
        1.0,
        "lightyellow",
        porosity_text,
        8,
    )

    # Risk classification (rectangle)
    risk_text = "STEP 5: RISK CLASSIFICATION\n<100: LOW | 100-200: MODERATE | 200-300: HIGH | >300: VERY HIGH"
    draw_rounded_rect(
        boxes["risk"][0], boxes["risk"][1], 7.0, 0.9, "lightcoral", risk_text, 8.5
    )

    # End (terminal)
    draw_terminal(boxes["end"][0], boxes["end"][1], 1.5, 0.6, "lightgray", "END", 12)

    # Draw arrows between boxes
    positions = list(boxes.values())

    for i in range(len(positions) - 1):
        start_x, start_y = positions[i]
        end_x, end_y = positions[i + 1]

        # Calculate arrow start/end points
        start_y_arrow = start_y - 0.35
        end_y_arrow = end_y + 0.35

        # Different arrow style for diamond
        if i == 4:  # After porosity diamond
            start_y_arrow = start_y - 0.5

        arrow = FancyArrowPatch(
            (start_x, start_y_arrow),
            (end_x, end_y_arrow),
            arrowstyle="->",
            mutation_scale=20,
            linewidth=2,
            color="darkred",
            alpha=0.8,
        )
        ax.add_patch(arrow)

    # Add title
    ax.text(
        5,
        11.8,
        "Indoor Radon Concentration Estimation & Risk Classification",
        fontsize=14,
        weight="bold",
        ha="center",
        va="center",
        bbox=dict(
            boxstyle="round,pad=0.5",
            facecolor="white",
            edgecolor="black",
            linewidth=2,
            alpha=0.9,
        ),
    )

    # Add subtitle
    ax.text(
        5,
        11.4,
        "Physics-Informed Model for Multi-Storey Buildings",
        fontsize=10,
        ha="center",
        va="center",
        style="italic",
        color="gray",
    )

    plt.tight_layout()
    plt.savefig("radon_workflow.png", dpi=300, bbox_inches="tight", facecolor="white")
    plt.show()

    return fig


# Alternative: Simple console/terminal diagram (ASCII)
def print_workflow_diagram():
    """Print ASCII workflow diagram to console"""

    diagram = """
    ╔══════════════════════════════════════════════════════════════════════════════╗
    ║            INDOOR RADON CONCENTRATION ESTIMATION & RISK CLASSIFICATION       ║
    ╚══════════════════════════════════════════════════════════════════════════════╝

                                    ┌─────────────┐
                                    │   START     │
                                    └──────┬──────┘
                                           │
                                           ▼
                          ┌────────────────────────────────┐
                          │     INPUT PARAMETERS           │
                          │  • Ql (lithology source)       │
                          │  • Qf (active fault source)    │
                          │  • Qb (building material)      │
                          └───────────────┬────────────────┘
                                          │
                                          ▼
                          ┌────────────────────────────────┐
                          │  STEP 1: Q₀ = Ql + Qf          │
                          │  (Ground surface concentration)│
                          └───────────────┬────────────────┘
                                          │
                                          ▼
                          ┌────────────────────────────────┐
                          │  STEP 2: Q₁ = Ql + Qf + Qb     │
                          │  (Ground floor concentration)  │
                          └───────────────┬────────────────┘
                                          │
                                          ▼
                          ┌────────────────────────────────┐
                          │  STEP 3: Q₂ = Qb               │
                          │  (First floor & above)         │
                          └───────────────┬────────────────┘
                                          │
                                          ▼
                                    ◇───────────◇
                                   ╱             ╲
                                  ╱   STEP 4:     ╲
                                 │   POROSITY     │
                                 │   WEIGHTING    │
                                 │  n₀=1, n₁=1    │
                                 │  n₂=0, n₄=0    │
                                  ╲             ╱
                                   ╲           ╱
                                    ◇─────────◇
                                          │
                                          ▼
                          ┌────────────────────────────────┐
                          │  STEP 5: RISK CLASSIFICATION   │
                          ├────────────────────────────────┤
                          │  Q < 100    →  LOW (No action) │
                          │  100-200    →  MODERATE        │
                          │  200-300    →  HIGH            │
                          │  > 300      →  VERY HIGH       │
                          └───────────────┬────────────────┘
                                          │
                                          ▼
                                    ┌─────────────┐
                                    │    END      │
                                    └─────────────┘
    """
    print(diagram)


# Function to implement the actual algorithm
def estimate_radon_risk(
    Ql, Qf, Qb, floor_level, building_age=None, n0=1.0, n1=1.0, n2=0.0, n4=0.0
):
    """
    Estimate indoor radon concentration and classify risk

    Parameters:
    -----------
    Ql : float
        Radon from lithology (Bq/m³) - effective indoor contribution
    Qf : float
        Radon from active faults (Bq/m³) - effective indoor contribution
    Qb : float
        Radon from building materials (Bq/m³)
    floor_level : int
        Floor number (1 = ground floor, 2 = first floor, etc.)
    building_age : int, optional
        Year of construction (for age adjustment)
    n0, n1, n2, n4 : float
        Porosity parameters (0-1)

    Returns:
    --------
    dict with concentration and risk classification
    """

    # Calculate ground surface concentration
    Q0 = n0 * (Ql + Qf)

    # Calculate floor-specific concentration
    if floor_level == 1:
        Q_floor = n1 * n0 * (Ql + Qf) + n1 * Qb + n2 * Qb + n4 * Qb
    elif floor_level == 2:
        Q_floor = (1 - n2) * Qb + n3 * Qb + n5 * Qb if "n3" in locals() else Qb
        # Simplified: Q_floor = Qb
        Q_floor = Qb
    else:
        # Higher floors: reduced contribution from ground sources
        decay_factor = max(0.3, 1 - 0.12 * (floor_level - 2))
        Q_floor = Qb + (Ql + Qf) * decay_factor * 0.1  # Reduced ground contribution

    # Age adjustment (newer buildings have lower ventilation)
    if building_age:
        current_year = 2026
        age_years = current_year - building_age
        if age_years < 10:  # New building (tight envelope)
            Q_floor = Q_floor * 1.25
        elif age_years > 30:  # Old building (leaky)
            Q_floor = Q_floor * 0.85

    Q_floor = round(Q_floor, 1)

    # Risk classification (based on European reference level)
    if Q_floor < 100:
        risk = "LOW"
        color = "🟢"
        action = "No action needed"
        urgency = "None"
    elif Q_floor < 200:
        risk = "MODERATE"
        color = "🟡"
        action = "Periodic retesting (every 5-10 years)"
        urgency = "Low"
    elif Q_floor < 300:
        risk = "HIGH"
        color = "🟠"
        action = "Mitigation recommended (ventilation improvement)"
        urgency = "Medium"
    else:
        risk = "VERY HIGH"
        color = "🔴"
        action = "Immediate mitigation required (soil depressurization)"
        urgency = "High"

    return {
        "concentration": Q_floor,
        "risk_class": risk,
        "color_icon": color,
        "recommended_action": action,
        "urgency": urgency,
        "ground_surface": round(Q0, 1),
    }


# Additional: Simple text-based flowchart using functions
def print_simple_flow():
    """Print a very simple vertical flow diagram"""
    print("\n" + "=" * 50)
    print("   SIMPLE RADON ESTIMATION WORKFLOW")
    print("=" * 50)

    steps = [
        ("1", "INPUT Ql, Qf, Qb", "📊"),
        ("2", "Q₀ = Ql + Qf (ground surface)", "🌍"),
        ("3", "Q₁ = Ql + Qf + Qb (ground floor)", "🏠"),
        ("4", "Q₂ = Qb (upper floors)", "🏢"),
        ("5", "Apply porosity coefficients", "🔬"),
        ("6", "Classify risk level", "⚠️"),
        ("7", "Output recommendation", "✅"),
    ]

    for i, (num, desc, icon) in enumerate(steps):
        print(f"\n   {icon}  STEP {num}: {desc}")
        if i < len(steps) - 1:
            print("      │")
            print("      ▼")

    print("\n" + "=" * 50)


# Example usage
if __name__ == "__main__":
    # Print simple flow first
    print_simple_flow()

    # Draw graphical diagram (uncomment if you have matplotlib properly installed)
    try:
        fig = draw_workflow_diagram()
        print("\n✅ Graphical diagram saved as 'radon_workflow.png'")
    except Exception as e:
        print(f"\n⚠️ Could not generate graphical diagram: {e}")
        print("   Displaying ASCII diagram instead...")
        print_workflow_diagram()

    # Example calculations for İzmir buildings
    print("\n" + "=" * 60)
    print("EXAMPLE CALCULATIONS FOR İZMİR BUILDINGS")
    print("=" * 60)
    print("(Based on field data from 117 buildings)")
    print("-" * 60)

    # Typical effective values for İzmir (derived from field measurements)
    examples = [
        {
            "name": "Bayraklı - Ground floor",
            "Ql": 100,
            "Qf": 25,
            "Qb": 115,
            "floor": 1,
            "age": 1995,
        },
        {
            "name": "Karşıyaka - Ground floor",
            "Ql": 110,
            "Qf": 30,
            "Qb": 115,
            "floor": 1,
            "age": 1988,
        },
        {
            "name": "Buca - Ground floor",
            "Ql": 150,
            "Qf": 35,
            "Qb": 115,
            "floor": 1,
            "age": 2015,
        },
        {
            "name": "Bornova - 3rd floor",
            "Ql": 140,
            "Qf": 30,
            "Qb": 115,
            "floor": 3,
            "age": 2010,
        },
        {
            "name": "Any district - 8th floor",
            "Ql": 140,
            "Qf": 30,
            "Qb": 115,
            "floor": 8,
            "age": 2005,
        },
    ]

    for ex in examples:
        result = estimate_radon_risk(
            ex["Ql"], ex["Qf"], ex["Qb"], ex["floor"], ex["age"]
        )
        print(f"\n{result['color_icon']} {ex['name']}")
        print(f"   📍 Floor: {ex['floor']} | Built: {ex['age']}")
        print(f"   📊 Estimated IRC: {result['concentration']} Bq/m³")
        print(f"   ⚠️ Risk: {result['risk_class']} (Urgency: {result['urgency']})")
        print(f"   🔧 Action: {result['recommended_action']}")

    print("\n" + "=" * 60)
    print("REFERENCE LEVELS:")
    print("  🟢 <100 Bq/m³  : WHO optimal level")
    print("  🟡 100-200 Bq/m³: Acceptable with monitoring")
    print("  🟠 200-300 Bq/m³: Exceeds European reference")
    print("  🔴 >300 Bq/m³  : Significant health risk")
    print("=" * 60)
    print("\n✅ Workflow diagram and estimation complete!")

XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
