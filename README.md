# OpenWebUI-Theme-Generator-Prompt

## Overview

This comprehensive prompt walks an agent through creating custom themes for OpenWebUI.

---

## Theme Creation Workflow

### 1. Conceptualization & Requirements

```python
class ThemeConcept:
    """
    Establishes the foundational vision and requirements for a new theme.
    """
    
    def __init__(self):
        self.concept = {
            'name': '',
            'description': '',
            'target_audience': '',
            'primary_use_case': '',
            'emotional_tone': '',
            'inspiration_sources': [],
            'key_features': []
        }
    
    def gather_requirements(self) -> 'ThemeConcept':
        """
        Collect core theme requirements through structured questioning.
        """
        print("=== THEME CONCEPTUALIZATION ===")
        
        self.concept['name'] = input("What is the name of your theme? ")
        
        self.concept['description'] = input(
            "Brief description of your theme: "
        )
        
        self.concept['target_audience'] = input(
            "Who is this theme designed for? "
        )
        
        self.concept['primary_use_case'] = input(
            "What is the main use case for this theme? "
        )
        
        emotional_tone_options = """
        Emotional tone options:
        1. Professional/Modern
        2. Creative/Artistic
        3. Minimalist/Clean
        4. Bold/Statement
        5. Warm/Cozy
        6. Cool/Professional
        7. Playful/Energy
        8. Classic/Timeless
        """
        print(emotional_tone_options)
        self.concept['emotional_tone'] = input("Select emotional tone: ")
        
        return self

    def define_features(self) -> 'ThemeConcept':
        """
        Identify key features and differentiation points.
        """
        print("\n=== KEY FEATURES ===")
        print("Enter features (type 'done' to finish):")
        
        while True:
            feature = input("Feature: ")
            if feature.lower() == 'done':
                break
            if feature.strip():
                self.concept['key_features'].append(feature)
        
        return self
```

### 2. Color Palette Configuration

```python
class ColorPalette:
    """
    Manages theme color configuration with accessibility validation.
    """
    
    def __init__(self, name: str, primary: str, secondary: str = None, **kwargs):
        self.name = name
        self.primary = self._validate_color(primary, "primary")
        self.secondary = self._validate_color(secondary, "secondary") if secondary else None
        self.accent = kwargs.get("accent")
        self.background = kwargs.get("background", "#ffffff")
        self.text = kwargs.get("text", "#000000")
        
        self.validation = {
            "primary_valid": True,
            "secondary_valid": True,
            "accessibility_meets_aa": True,
            "accessibility_meets_aaa": True
        }
    
    def _validate_color(self, color: str, color_type: str) -> str:
        """Validate a color string and return default if invalid."""
        if not color:
            return self._get_default_for_type(color_type)
        
        # Basic color format validation
        if not (color.startswith(("rgb(", "rgba(", "hsl(", "hsla(", "#"))):
            print(f"Warning: Invalid color format for {color_type}. Using default.")
            return self._get_default_for_type(color_type)
        
        return color
    
    def _get_default_for_type(self, color_type: str) -> str:
        """Return default color based on type."""
        defaults = {
            "primary": "#6366f1",
            "secondary": "#8b5cf6",
            "accent": "#ec4899",
            "background": "#ffffff",
            "text": "#000000"
        }
        return defaults.get(color_type, defaults["primary"])
    
    def check_accessibility(self) -> Dict[str, bool]:
        """Check accessibility criteria (WCAG 2.1 AA/AAA)."""
        results = {
            "meets_aa": True,
            "meets_aaa": True,
            "tests_run": 0,
            "failed_tests": []
        }
        
        contrast_checks = self._check_contrast_ratios()
        results["tests_run"] += len(contrast_checks)
        
        if not all(contrast_checks.values()):
            results["meets_aa"] = False
            results["meets_aaa"] = False
            
            for test, passes in contrast_checks.items():
                if not passes:
                    results["failed_tests"].append(test)
        
        return results
    
    def _check_contrast_ratios(self) -> Dict[str, bool]:
        """Check contrast ratios between text and background colors."""
        checks = {
            "text_on_background_aa": False,
            "text_on_background_aaa": False,
            "primary_on_background_aa": False,
            "primary_on_background_aaa": False,
            "secondary_on_background_aa": False,
            "secondary_on_background_aaa": False
        }
        
        # Contrast ratio calculation
        def contrast_ratio(c1: str, c2: str) -> float:
            """Calculate contrast ratio between two colors (WCAG 2.1)."""
            try:
                from colorcontrast import contrast_ratio as cr
                return cr(c1, c2)
            except:
                return 1.0
        
        # Text on background
        text_color = self.text
        bg_color = self.background
        
        ratio = contrast_ratio(text_color, bg_color)
        checks["text_on_background_aa"] = ratio >= 4.5
        checks["text_on_background_aaa"] = ratio >= 7.0
        
        # Primary on background
        if self.primary:
            ratio = contrast_ratio(self.primary, bg_color)
            checks["primary_on_background_aa"] = ratio >= 3.0
            checks["primary_on_background_aaa"] = ratio >= 4.5
        
        # Secondary on background
        if self.secondary:
            ratio = contrast_ratio(self.secondary, bg_color)
            checks["secondary_on_background_aa"] = ratio >= 3.0
            checks["secondary_on_background_aaa"] = ratio >= 4.5
        
        return checks
    
    def to_dict(self) -> Dict[str, Any]:
        """Convert palette to dictionary with validation status."""
        return {
            **self.__dict__,
            "validation": self.validation,
            "accessibility": self.check_accessibility()
        }
    
    def __str__(self) -> str:
        """Return string representation of palette."""
        return json.dumps(self.to_dict(), indent=2)
```

### 3. Typography Configuration

```python
class Typography:
    """
    Manages font configuration and readability validation.
    """
    
    DEFAULT_FONTS = {
        "primary": "Inter",
        "secondary": "Poppins",
        "mono": "Courier New",
        "display": "Playfair Display"
    }
    
    def __init__(
        self,
        primary_font: str = None,
        secondary_font: str = None,
        mono_font: str = None,
        display_font: str = None,
        **kwargs
    ):
        self.settings = {
            "primary": self._get_font_config(primary_font, "primary"),
            "secondary": self._get_font_config(secondary_font, "secondary"),
            "mono": self._get_font_config(mono_font, "mono"),
            "display": self._get_font_config(display_font, "display"),
            **kwargs
        }
    
    def _get_font_config(
        self,
        font_name: str,
        font_type: str
    ) -> Dict[str, Any]:
        """Get font configuration with defaults."""
        if not font_name:
            return self._get_default_font_config(font_type)
        
        # Font configuration mapping
        font_configs = {
            "primary": {
                "family": "Inter",
                "weights": [400, 500, 600, 700],
                "styles": ["normal", "italic"],
                "sizes": {
                    "body": "16px",
                    "heading": "24px",
                    "subheading": "20px"
                }
            },
            "secondary": {
                "family": "Poppins",
                "weights": [400, 500, 600, 700, 800],
                "styles": ["normal", "italic"],
                "sizes": {
                    "body": "14px",
                    "heading": "22px",
                    "subheading": "18px"
                }
            },
            "mono": {
                "family": "Courier New",
                "weights": [400, 700],
                "styles": ["normal"],
                "sizes": {
                    "body": "14px",
                    "code": "16px"
                }
            },
            "display": {
                "family": "Playfair Display",
                "weights": [400, 700],
                "styles": ["normal", "italic"],
                "sizes": {
                    "headline": "48px",
                    "title": "36px"
                }
            }
        }
        
        return font_configs.get(font_type, font_configs["primary"])
    
    def _get_default_font_config(self, font_type: str) -> Dict[str, Any]:
        """Get default font configuration."""
        defaults = {
            "family": self.DEFAULT_FONTS.get(font_type, self.DEFAULT_FONTS["primary"]),
            "weights": [400, 700],
            "styles": ["normal"],
            "sizes": {
                "body": "16px"
            }
        }
        return defaults
    
    def validate_font_sizes(self) -> Dict[str, bool]:
        """Validate font sizes for readability."""
        validation = {
            "valid": True,
            "issues": []
        }
        
        min_readable_size = 12
        max_readable_size = 32
        
        for font_type, font_config in self.settings.items():
            for size_name, size_value in font_config["sizes"].items():
                try:
                    size_px = int(size_value.strip("px"))
                    if size_px < min_readable_size:
                        validation["issues"].append({
                            "font": font_type,
                            "size": f"{size_px}px",
                            "issue": f"Too small (must be ≥ {min_readable_size}px)",
                            "recommendation": f"Consider {min_readable_size}px or larger"
                        })
                        validation["valid"] = False
                    
                    if size_px > max_readable_size:
                        validation["issues"].append({
                            "font": font_type,
                            "size": f"{size_px}px",
                            "issue": f"Too large (should be ≤ {max_readable_size}px)",
                            "recommendation": f"Consider {max_readable_size}px or smaller"
                        })
                        validation["valid"] = False
                
                except ValueError:
                    validation["issues"].append({
                        "font": font_type,
                        "size": size_value,
                        "issue": "Invalid size format",
                        "recommendation": "Use format like '16px' or '1.2rem'"
                    })
                    validation["valid"] = False
        
        return validation
    
    def get_typography_css(self) -> str:
        """Generate CSS for typography configuration."""
        css = ""
        
        for font_type, font_config in self.settings.items():
            family = font_config["family"]
            weights = font_config["weights"]
            styles = font_config["styles"]
            
            css += f"/* {font_type.capitalize()} Font */\n"
            
            # Font family
            css += f"body.{font_type} {\n"
            css += f'  font-family: "{family}", sans-serif;\n'
            css += "}\n"
            
            # Font weights
            for weight in weights:
                css += f".{font_type}-weight-{weight} {\n"
                css += f"  font-weight: {weight};\n"
                css += "}\n"
            
            # Font styles
            for style in styles:
                css += f".{font_type}-style-{style} {\n"
                css += f"  font-style: {style};\n"
                css += "}\n"
            
            # Specific sizes
            for size_name, size_value in font_config["sizes"].items():
                css += f".{font_type}-{size_name} {\n"
                css += f"  font-size: {size_value};\n"
                css += "}\n"
            
            css += "\n"
        
        return css
```

### 4. Component Styling

```python
class ComponentStyles:
    """
    Defines and manages styles for UI components.
    """
    
    DEFAULT_COMPONENT_STYLES = {
        "button": {
            "base": {
                "padding": "12px 24px",
                "border_radius": "8px",
                "transition": "all 0.3s ease"
            },
            "primary": {
                "background": "var(--primary)",
                "color": "var(--text)",
                "border": "none",
                "&:hover": {
                    "background": "var(--primary-dark)"
                }
            },
            "secondary": {
                "background": "var(--secondary)",
                "color": "var(--text)",
                "border": "none",
                "&:hover": {
                    "background": "var(--secondary-dark)"
                }
            },
            "outline": {
                "background": "transparent",
                "color": "var(--primary)",
                "border": "2px solid var(--primary)",
                "&:hover": {
                    "background": "var(--primary-light)"
                }
            }
        },
        "card": {
            "base": {
                "padding": "24px",
                "margin_bottom": "16px",
                "border_radius": "12px",
                "box_shadow": "0 4px 20px rgba(0,0,0,0.1)"
            },
            "elevated": {
                "box_shadow": "0 8px 30px rgba(0,0,0,0.2)"
            },
            "outlined": {
                "border": "1px solid var(--border)",
                "box_shadow": "none"
            }
        },
        "input": {
            "base": {
                "padding": "12px 16px",
                "border": "1px solid var(--border)",
                "border_radius": "6px",
                "&:focus": {
                    "outline": "none",
                    "border_color": "var(--primary)"
                }
            },
            "danger": {
                "border_color": "var(--error)",
                "&:focus": {
                    "border_color": "var(--error-dark)"
                }
            }
        }
    }
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.styles = self.DEFAULT_COMPONENT_STYLES.copy()
    
    def apply_theme(self) -> 'ComponentStyles':
        """
        Apply theme configuration to component styles.
        """
        for component, style_config in self.styles.items():
            if component in self.theme_config.get("components", {}):
                component_config = self.theme_config["components"][component]
                
                if "base" in component_config:
                    self.styles[component]["base"].update(component_config["base"])
                
                if "variants" in component_config:
                    for variant, variant_config in component_config["variants"].items():
                        if variant not in self.styles[component]:
                            self.styles[component][variant] = {}
                        
                        self.styles[component][variant].update(variant_config)
        
        return self
    
    def generate_css(self) -> str:
        """Generate CSS from component styles."""
        css = ""
        
        for component, component_styles in self.styles.items():
            css += f"/* {component.capitalize()} Styles */\n"
            
            for variant, variant_styles in component_styles.items():
                if variant == "base":
                    css += f".{component} {\n"
                    for key, value in variant_styles.items():
                        css += f"  {key}: {value};\n"
                    css += "}\n\n"
                
                else:
                    css += f".{component}-{variant} {\n"
                    for key, value in variant_styles.items():
                        css += f"  {key}: {value};\n"
                    css += "}\n\n"
            
            css += "\n"
        
        return css
```

### 5. Advanced Features

```python
class AdvancedFeatures:
    """
    Handles animations, transitions, and special effects.
    """
    
    ANIMATION_OPTIONS = {
        "fade_in": "opacity: 0; transform: translateY(20px);",
        "slide_in": "opacity: 0; transform: translateX(-50px);",
        "zoom_in": "opacity: 0; transform: scale(0.8);",
        "bounce_in": "opacity: 0; transform: scale(0.5); animation-timing-function: cubic-bezier(0.8, 0, 1, 1);"
    }
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.animations = {}
        self.transitions = {}
    
    def define_animations(self) -> 'AdvancedFeatures':
        """
        Define custom animations for the theme.
        """
        print("\n=== ANIMATION DEFINITIONS ===")
        print("Available options:")
        
        for i, (name, code) in enumerate(self.ANIMATION_OPTIONS.items(), 1):
            print(f"{i}. {name}")
        
        while True:
            choice = input("Enter animation name (or 'done'): ")
            if choice.lower() == 'done':
                break
            
            if choice in self.ANIMATION_OPTIONS:
                animation_key = f"animation-{choice}"
                self.animations[animation_key] = self.ANIMATION_OPTIONS[choice]
                print(f"Added animation: {animation_key}")
        
        return self
    
    def define_transitions(self) -> 'AdvancedFeatures':
        """
        Define custom transitions for UI elements.
        """
        transition_properties = [
            "opacity",
            "transform",
            "background-color",
            "color",
            "border-color",
            "box-shadow"
        ]
        
        print("\n=== TRANSITION DEFINITIONS ===")
        print("Select properties to include (separated by spaces):")
        print("Available:", ", ".join(transition_properties))
        
        selected_properties = input("Properties: ").split()
        duration = input("Duration (e.g., 0.3s): ") or "0.3s"
        timing_function = input("Timing function (e.g., ease): ") or "ease"
        
        transition_value = " ".join(selected_properties) + " " + duration + " " + timing_function
        
        self.transitions["default-transition"] = transition_value
        print(f"Added transition: default-transition({transition_value})")
        
        return self
    
    def generate_animation_css(self) -> str:
        """Generate CSS for animations."""
        css = ""
        
        for animation_key, animation_code in self.animations.items():
            css += f"/* {animation_key} animation */\n"
            css += f"{animation_key} {{\n"
            css += f"  {animation_code}\n"
            css += "  animation-fill-mode: both;\n"
            css += "  animation-duration: 0.5s;\n"
            css += "  animation-timing-function: ease;\n"
            css += "}\n\n"
        
        css += "/* Transition utilities */\n"
        for transition_key, transition_value in self.transitions.items():
            css += f"{transition_key} {{\n"
            css += f"  transition: {transition_value};\n"
            css += "}\n"
        
        return css
```

### 6. Accessibility Implementation

```python
class Accessibility:
    """
    Ensures the theme meets accessibility standards.
    """
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.accessibility_requirements = {
            "color_contrast_aa": "4.5:1",
            "color_contrast_aaa": "7.0:1",
            "font_size_min": 12,
            "font_size_max": 32,
            "line_height_min_ratio": 1.2,
            "line_height_max_ratio": 2.0,
            "letter_spacing_max": 2,
            "text_transform_limit": 0.25
        }
    
    def check_all_requirements(self) -> Dict[str, bool]:
        """Check all accessibility requirements."""
        results = {
            "all_meet_aa": True,
            "all_meet_aaa": True,
            "failed_tests": []
        }
        
        # Check color contrast
        contrast_results = self.check_color_contrast()
        if not contrast_results["meets_aa"]:
            results["all_meet_aa"] = False
            results["failed_tests"].extend(contrast_results["failed_tests"])
        
        if not contrast_results["meets_aaa"]:
            results["all_meet_aaa"] = False
        
        # Check font sizes
        font_results = self.check_font_sizes()
        if not font_results["meets_aa"]:
            results["all_meet_aa"] = False
            results["failed_tests"].extend(font_results["failed_tests"])
        
        # Check line height
        line_height_results = self.check_line_height()
        if not line_height_results["meets_aa"]:
            results["all_meet_aa"] = False
            results["failed_tests"].extend(line_height_results["failed_tests"])
        
        return results
    
    def check_color_contrast(self) -> Dict[str, bool]:
        """Check color contrast ratios."""
        from colorcontrast import contrast_ratio
        
        results = {
            "meets_aa": True,
            "meets_aaa": True,
            "failed_tests": []
        }
        
        # Text on background
        text_color = self.theme_config.get("colors", {}).get("text", "#000000")
        bg_color = self.theme_config.get("colors", {}).get("background", "#ffffff")
        
        ratio = contrast_ratio(text_color, bg_color)
        if ratio < 4.5:
            results["meets_aa"] = False
            results["failed_tests"].append({
                "test": "Text on background",
                "ratio": round(ratio, 2),
                "required_aa": 4.5,
                "required_aaa": 7.0
            })
        
        if ratio < 7.0:
            results["meets_aaa"] = False
        
        # Primary button
        if "primary" in self.theme_config.get("colors", {}):
            primary_color = self.theme_config["colors"]["primary"]
            ratio = contrast_ratio(primary_color, bg_color)
            if ratio < 3.0:
                results["meets_aa"] = False
                results["failed_tests"].append({
                    "test": "Primary button",
                    "ratio": round(ratio, 2),
                    "required_aa": 3.0,
                    "required_aaa": 4.5
                })
            
            if ratio < 4.5:
                results["meets_aaa"] = False
        
        return results
    
    def check_font_sizes(self) -> Dict[str, bool]:
        """Check font sizes against readability guidelines."""
        results = {
            "meets_aa": True,
            "failed_tests": []
        }
        
        typography = self.theme_config.get("typography", {})
        
        for font_type, font_config in typography.items():
            for size_name, size_value in font_config.get("sizes", {}).items():
                try:
                    size_px = int(size_value.strip("px"))
                    
                    if size_px < 12:
                        results["meets_aa"] = False
                        results["failed_tests"].append({
                            "element": f"{font_type} {size_name}",
                            "current_size": f"{size_px}px",
                            "issue": "Too small for readability",
                            "recommendation": "Minimum 12px"
                        })
                    
                    if size_px > 32:
                        results["meets_aa"] = False
                        results["failed_tests"].append({
                            "element": f"{font_type} {size_name}",
                            "current_size": f"{size_px}px",
                            "issue": "Too large for typical reading",
                            "recommendation": "Consider 32px maximum"
                        })
                
                except (ValueError, KeyError):
                    continue
        
        return results
    
    def check_line_height(self) -> Dict[str, bool]:
        """Check line height ratios."""
        results = {
            "meets_aa": True,
            "failed_tests": []
        }
        
        typography = self.theme_config.get("typography", {})
        
        for font_type, font_config in typography.items():
            if "line_height" in font_config:
                try:
                    line_height = float(font_config["line_height"])
                    font_size = float(font_config.get("sizes", {}).get("body", 16))
                    
                    ratio = line_height / font_size
                    
                    if ratio < 1.2:
                        results["meets_aa"] = False
                        results["failed_tests"].append({
                            "element": font_type,
                            "current_ratio": round(ratio, 2),
                            "issue": "Line height too small",
                            "recommendation": "Minimum 1.2 ratio"
                        })
                    
                    if ratio > 2.0:
                        results["meets_aa"] = False
                        results["failed_tests"].append({
                            "element": font_type,
                            "current_ratio": round(ratio, 2),
                            "issue": "Line height too large",
                            "recommendation": "Maximum 2.0 ratio"
                        })
                
                except (ValueError, ZeroDivisionError):
                    continue
        
        return results
    
    def add_accessibility_classes(self) -> Dict[str, str]:
        """
        Generate accessibility-enhancing CSS classes.
        """
        return {
            "keyboard-focus": """
                outline: 2px solid var(--primary);
                outline-offset: 2px;
                transition: outline 0.2s ease;
            """,
            "focus-visible": """
                &:focus-visible {
                    outline: 3px solid var(--primary-dark);
                    outline-offset: 3px;
                }
            """,
            "aria-label": """
                &[aria-label] {
                    cursor: pointer;
                }
            """,
            "screen-reader-only": """
                position: absolute;
                width: 1px;
                height: 1px;
                padding: 0;
                margin: -1px;
                overflow: hidden;
                clip: rect(0 0 0 0);
                border: 0;
            """
        }
```

### 7. Responsive Design

```python
class ResponsiveDesign:
    """
    Manages responsive breakpoints and media queries.
    """
    
    DEFAULT_BREAKPOINTS = {
        "mobile": "max-width: 768px",
        "tablet": "max-width: 1024px",
        "laptop": "max-width: 1440px",
        "desktop": "min-width: 1441px"
    }
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.breakpoints = self.DEFAULT_BREAKPOINTS.copy()
        self.media_queries = {}
    
    def define_breakpoints(self) -> 'ResponsiveDesign':
        """
        Customize responsive breakpoints.
        """
        print("\n=== BREAKPOINT CUSTOMIZATION ===")
        print("Current breakpoints:")
        
        for name, query in self.breakpoints.items():
            print(f"- {name}: {query}")
        
        while True:
            action = input("Enter breakpoint name to modify or 'done': ").lower()
            if action == 'done':
                break
            
            if action in self.breakpoints:
                new_query = input(f"New query for {action}: ")
                if new_query:
                    self.breakpoints[action] = new_query
                    print(f"Updated {action} breakpoint")
            
            else:
                print(f"Unknown breakpoint: {action}")
        
        return self
    
    def create_media_queries(self) -> 'ResponsiveDesign':
        """
        Generate media queries for responsive design.
        """
        self.media_queries = {
            "mobile": f"@media {self.breakpoints['mobile']} {",
            "tablet": f"@media {self.breakpoints['tablet']} {",
            "laptop": f"@media {self.breakpoints['laptop']} {",
            "desktop": f"@media {self.breakpoints['desktop']} {"
        }
        
        return self
    
    def add_layout_rules(self) -> 'ResponsiveDesign':
        """
        Add responsive layout rules.
        """
        layout_rules = {
            "container": """
                max-width: 100%;
                padding: 24px;
                margin: 0 auto;
            """,
            "grid-columns": """
                grid-template-columns: 1fr;
            """,
            "sidebar-hidden": """
                display: none;
            """,
            "header-expanded": """
                padding: 32px;
            """
        }
        
        self.media_queries["mobile"].update(layout_rules)
        
        layout_rules["grid-columns"] = "grid-template-columns: 1fr 2fr;"
        layout_rules["sidebar-hidden"] = "display: block;"
        layout_rules["header-expanded"] = "padding: 24px;"
        
        self.media_queries["tablet"].update(layout_rules)
        
        layout_rules["grid-columns"] = "grid-template-columns: repeat(3, 1fr);"
        layout_rules["sidebar-hidden"] = "display: block;"
        
        self.media_queries["laptop"].update(layout_rules)
        
        layout_rules["grid-columns"] = "grid-template-columns: repeat(4, 1fr);"
        
        self.media_queries["desktop"].update(layout_rules)
        
        return self
    
    def generate_responsive_css(self) -> str:
        """Generate complete responsive design CSS."""
        css = ""
        
        for breakpoint, queries in self.media_queries.items():
            css += f"/* {breakpoint.capitalize()} Styles */\n"
            css += f"{queries}\n"
        
        return css
```

### 8. Theme Preview & Iteration

```python
class ThemePreview:
    """
    Handles theme preview and iteration.
    """
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.preview_state = {
            "active": True,
            "changes_pending": False,
            "validation_results": {}
        }
    
    def start_preview(self) -> 'ThemePreview':
        """
        Start the theme preview server.
        """
        import threading
        from http.server import SimpleHTTPRequestHandler, HTTPServer
        
        class ThemePreviewHandler(SimpleHTTPRequestHandler):
            def do_GET(self):
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                self.wfile.write(bytes(self._get_preview_html(), 'utf-8'))
            
            def _get_preview_html(self) -> str:
                html = """
                    <!DOCTYPE html>
                    <html lang="en">
                    <head>
                        <meta charset="UTF-8">
                        <meta name="viewport" content="width=device-width, initial-scale=1.0">
                        <title>Theme Preview</title>
                        <style>
                            body {
                                font-family: var(--primary-font);
                                padding: 24px;
                                background: var(--background);
                                color: var(--text);
                            }
                            
                            .preview-container {
                                max-width: 1200px;
                                margin: 0 auto;
                            }
                            
                            h1 {
                                font-size: var(--heading-size);
                                margin-bottom: 24px;
                            }
                            
                            .section {
                                margin-bottom: 36px;
                                padding: 24px;
                                background: var(--card-background);
                                border-radius: 12px;
                                border: 1px solid var(--border);
                            }
                            
                            .section-title {
                                font-size: 20px;
                                font-weight: 600;
                                margin-bottom: 16px;
                                color: var(--text-secondary);
                            }
                            
                            button {
                                padding: 12px 24px;
                                margin-right: 8px;
                                margin-bottom: 8px;
                                font-family: inherit;
                            }
                            
                            .primary-button {
                                background: var(--primary);
                                color: var(--text);
                            }
                            
                            .secondary-button {
                                background: var(--secondary);
                                color: var(--text);
                            }
                            
                            .outline-button {
                                background: transparent;
                                color: var(--primary);
                                border: 2px solid var(--primary);
                            }
                        </style>
                    </head>
                    <body>
                        <div class="preview-container">
                            <h1>Theme Preview</h1>
                            
                            <div class="section">
                                <div class="section-title">Colors</div>
                                <div style="display: flex; gap: 24px; flex-wrap: wrap;">
                                    <div style="flex: 1; min-width: 200px;">
                                        <div style="background: var(--primary); padding: 24px; border-radius: 8px;">Primary</div>
                                        <div style="color: var(--text-on-primary); padding: 24px; border-radius: 8px; background: var(--primary);">Text on Primary</div>
                                    </div>
                                    <div style="flex: 1; min-width: 200px;">
                                        <div style="background: var(--secondary); padding: 24px; border-radius: 8px;">Secondary</div>
                                        <div style="color: var(--text-on-secondary); padding: 24px; border-radius: 8px; background: var(--secondary);">Text on Secondary</div>
                                    </div>
                                </div>
                            </div>
                            
                            <div class="section">
                                <div class="section-title">Buttons</div>
                                <button class="primary-button">Primary Button</button>
                                <button class="secondary-button">Secondary Button</button>
                                <button class="outline-button">Outline Button</button>
                            </div>
                            
                            <div class="section">
                                <div class="section-title">Cards</div>
                                <div style="background: var(--card-background); padding: 24px; border-radius: 12px; border: 1px solid var(--border);">
                                    <h3>Sample Card</h3>
                                    <p>This is a sample card demonstrating your theme's card styling.</p>
                                </div>
                            </div>
                        </div>
                    </body>
                    </html>
                """
                return html
        
        httpd = HTTPServer(('localhost', 8000), ThemePreviewHandler)
        print("Starting theme preview on port 8000...")
        httpd.serve_forever()
    
    def apply_changes(self, changes: Dict[str, Any]) -> 'ThemePreview':
        """
        Apply changes and refresh preview.
        """
        self.theme_config.update(changes)
        self.preview_state["changes_pending"] = False
        print("Applied changes. Refreshing preview...")
        return self
    
    def validate_preview(self) -> Dict[str, bool]:
        """
        Validate the preview state.
        """
        validation = {
            "preview_active": True,
            "errors_found": False,
            "warnings": []
        }
        
        # Check if theme configuration is valid
        if not self.theme_config.get("colors"):
            validation["errors_found"] = True
            validation["warnings"].append("No colors defined in theme configuration")
        
        if not self.theme_config.get("typography"):
            validation["warnings"].append("No typography defined")
        
        return validation
```

### 9. Theme Deployment

```python
class ThemeDeployment:
    """
    Handles theme packaging and deployment.
    """
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.build_config = {
            "output_format": "json",
            "minify": False,
            "include_source_maps": False
        }
    
    def package_theme(self) -> Dict[str, str]:
        """
        Package the theme into distributable format.
        """
        from datetime import datetime
        import json
        
        package = {
            "theme": self.theme_config,
            "metadata": {
                "created_at": datetime.now().isoformat(),
                "version": "1.0.0",
                "name": self.theme_config.get("name", "untitled-theme")
            }
        }
        
        if self.build_config["output_format"] == "json":
            return json.dumps(package, indent=2)
        
        # This is a simplified example - real implementation would be more complex
        return package
    
    def generate_theme_json(self) -> str:
        """
        Generate complete theme JSON file.
        """
        theme_json = {
            "name": self.theme_config.get("name", "default-theme"),
            "description": self.theme_config.get("description", "Default theme"),
            "colors": self.theme_config.get("colors", {}),
            "typography": self.theme_config.get("typography", {}),
            "components": self.theme_config.get("components", {}),
            "accessibility": {
                "contrast": self.theme_config.get("accessibility", {}).get("contrast", {}),
                "keyboard": self.theme_config.get("accessibility", {}).get("keyboard", {})
            },
            "breakpoints": self.theme_config.get("responsive", {}).get("breakpoints", {}),
            "animations": self.theme_config.get("animations", {})
        }
        
        return json.dumps(theme_json, indent=2)
    
    def create_theme_zip(self) -> str:
        """
        Create a ZIP archive of the theme.
        """
        import zipfile
        import os
        
        zip_filename = f"{self.theme_config.get('name', 'theme')}.zip"
        files_to_include = [
            "theme.json",
            "style.css",
            "preview.html",
            "icon.png"
        ]
        
        with zipfile.ZipFile(zip_filename, 'w') as zipf:
            for file in files_to_include:
                zipf.write(file, arcname=file)
        
        return zip_filename
    
    def install_theme(self, theme_path: str) -> bool:
        """
        Install a theme from file path.
        """
        try:
            with open(theme_path, 'r') as file:
                theme_data = json.load(file)
            
            # Apply theme configuration
            self.theme_config.update(theme_data.get("theme", {}))
            
            print(f"Successfully installed theme: {theme_data.get('name', 'untitled')}")
            return True
        
        except Exception as e:
            print(f"Failed to install theme: {str(e)}")
            return False
```

### 10. Error Handling & Validation

```python
class ThemeValidator:
    """
    Central validation system for theme configurations.
    """
    
    def __init__(self, theme_config: Dict[str, Any]):
        self.theme_config = theme_config
        self.validation_results = {
            "overall": {"valid": True, "errors": [], "warnings": []},
            "sections": {}
        }
    
    def validate_all(self) -> Dict[str, Any]:
        """Validate the entire theme configuration."""
        self.validation_results["sections"] = {
            "colors": self.validate_colors(),
            "typography": self.validate_typography(),
            "components": self.validate_components(),
            "accessibility": self.validate_accessibility(),
            "responsive": self.validate_responsive()
        }
        
        self._calculate_overall_status()
        return self.validation_results
    
    def _calculate_overall_status(self):
        """Calculate overall validation status."""
        self.validation_results["overall"]["valid"] = all(
            section["valid"] 
            for section in self.validation_results["sections"].values()
        )
        
        self.validation_results["overall"]["errors"] = [
            error for section in self.validation_results["sections"].values()
            for error in section.get("errors", [])
        ]
        
        self.validation_results["overall"]["warnings"] = [
            warning for section in self.validation_results["sections"].values()
            for warning in section.get("warnings", [])
        ]
    
    def validate_colors(self) -> Dict[str, Any]:
        """Validate color configuration."""
        section_results = {"valid": True, "errors": [], "warnings": []}
        colors = self.theme_config.get("colors", {})
        
        if not colors:
            section_results["valid"] = False
            section_results["errors"].append("No colors defined")
            return section_results
        
        # Check primary color
        if "primary" not in colors:
            section_results["valid"] = False
            section_results["errors"].append("Missing primary color")
        
        # Check accessibility
        accessibility = self.validate_color_accessibility(colors)
        if not accessibility["meets_aa"]:
            section_results["valid"] = False
            section_results["errors"].extend(accessibility["failed_tests"])
        
        return section_results
    
    def validate_color_accessibility(self, colors: Dict[str, str]) -> Dict[str, bool]:
        """Validate color accessibility."""
        from colorcontrast import contrast_ratio
        
        results = {
            "meets_aa": True,
            "meets_aaa": True,
            "failed_tests": []
        }
        
        # Text on background
        text_color = colors.get("text", "#000000")
        bg_color = colors.get("background", "#ffffff")
        ratio = contrast_ratio(text_color, bg_color)
        
        if ratio < 4.5:
            results["meets_aa"] = False
            results["failed_tests"].append({
                "test": "Text on background",
                "ratio": round(ratio, 2),
                "required_aa": 4.5,
                "required_aaa": 7.0
            })
        
        if ratio < 7.0:
            results["meets_aaa"] = False
        
        # Primary button
        if "primary" in colors:
            primary_color = colors["primary"]
            ratio = contrast_ratio(primary_color, bg_color)
            
            if ratio < 3.0:
                results["meets_aa"] = False
                results["failed_tests"].append({
                    "test": "Primary button",
                    "ratio": round(ratio, 2),
                    "required_aa": 3.0,
                    "required_aaa": 4.5
                })
            
            if ratio < 4.5:
                results["meets_aaa"] = False
        
        return results
    
    def validate_typography(self) -> Dict[str, Any]:
        """Validate typography configuration."""
        section_results = {"valid": True, "errors": [], "warnings": []}
        typography = self.theme_config.get("typography", {})
        
        if not typography:
            section_results["valid"] = False
            section_results["errors"].append("No typography defined")
            return section_results
        
        # Validate font sizes
        font_size_results = self.validate_font_sizes(typography)
        if not font_size_results["meets_aa"]:
            section_results["valid"] = False
            section_results["errors"].extend(font_size_results["failed_tests"])
        
        # Validate line heights
        line_height_results = self.validate_line_heights(typography)
        if not line_height_results["meets_aa"]:
            section_results["valid"] = False
            section_results["errors"].extend(line_height_results["failed_tests"])
        
        return section_results
    
    def validate_font_sizes(self, typography: Dict[str, Any]) -> Dict[str, Any]:
        """Validate font size configuration."""
        results = {"meets_aa": True, "failed_tests": []}
        
        for font_type, font_config in typography.items():
            if "sizes" in font_config:
                for size_name, size_value in font_config["sizes"].items():
                    try:
                        size_px = int(size_value.strip("px"))
                        
                        if size_px < 12:
                            results["meets_aa"] = False
                            results["failed_tests"].append({
                                "element": f"{font_type} {size_name}",
                                "current_size": f"{size_px}px",
                                "issue": "Too small for readability",
                                "recommendation": "Minimum 12px"
                            })
                        
                        if size_px > 32:
                            results["meets_aa"] = False
                            results["failed_tests"].append({
                                "element": f"{font_type} {size_name}",
                                "current_size": f"{size_px}px",
                                "issue": "Too large for typical reading",
                                "recommendation": "Consider 32px maximum"
                            })
                    
                    except (ValueError, KeyError):
                        continue
        
        return results
    
    def validate_line_heights(self, typography: Dict[str, Any]) -> Dict[str, Any]:
        """Validate line height configuration."""
        results = {"meets_aa": True, "failed_tests": []}
        
        for font_type, font_config in typography.items():
            if "line_height" in font_config:
                try:
                    line_height = float(font_config["line_height"])
                    font_size = float(font_config.get("sizes", {}).get("body", 16))
                    
                    ratio = line_height / font_size
                    
                    if ratio < 1.2:
                        results["meets_aa"] = False
                        results["failed_tests"].append({
                            "element": font_type,
                            "current_ratio": round(ratio, 2),
                            "issue": "Line height too small",
                            "recommendation": "Minimum 1.2 ratio"
                        })
                    
                    if ratio > 2.0:
                        results["meets_aa"] = False
                        results["failed_tests"].append({
                            "element": font_type,
                            "current_ratio": round(ratio, 2),
                            "issue": "Line height too large",
                            "recommendation": "Maximum 2.0 ratio"
                        })
                
                except (ValueError, ZeroDivisionError):
                    continue
        
        return results
    
    def validate_components(self) -> Dict[str, Any]:
        """Validate component configurations."""
        section_results = {"valid": True, "errors": [], "warnings": []}
        components = self.theme_config.get("components", {})
        
        if not components:
            section_results["valid"] = False
            section_results["errors"].append("No components defined")
            return section_results
        
        for component, config in components.items():
            component_results = self.validate_individual_component(component, config)
            
            if not component_results["valid"]:
                section_results["valid"] = False
                section_results["errors"].extend(component_results["errors"])
        
        return section_results
    
    def validate_individual_component(
        self, component: str, config: Dict[str, Any]
    ) -> Dict[str, Any]:
        """Validate a single component configuration."""
        results = {"valid": True, "errors": []}
        
        # Check base configuration
        if "base" not in config:
            results["valid"] = False
            results["errors"].append(f"{component} missing base configuration")
        
        # Check required base properties
        required_base_props = ["padding", "border_radius"]
        for prop in required_base_props:
            if prop not in config["base"]:
                results["valid"] = False
                results["errors"].append(
                    f"{component} missing base property: {prop}"
                )
        
        return results
    
    def validate_accessibility(self) -> Dict[str, Any]:
        """Validate accessibility configuration."""
        section_results = {"valid": True, "errors": [], "warnings": []}
        accessibility = self.theme_config.get("accessibility", {})
        
        # Check keyboard accessibility
        if "keyboard" in accessibility:
            self.validate_keyboard_accessibility(accessibility["keyboard"])
        
        # Check ARIA configurations
        if "aria" in accessibility:
            self.validate_aria_config(accessibility["aria"])
        
        return section_results
    
    def validate_keyboard_accessibility(
        self, keyboard_config: Dict[str, Any]
    ) -> Dict[str, Any]:
        """Validate keyboard accessibility settings."""
        results = {"valid": True, "errors": []}
        
        # Check focus visibility
        if "focus_visible" not in keyboard_config:
            results["valid"] = False
            results["errors"].append("Missing focus visible style")
        
        # Check keyboard shortcuts
        if "shortcuts" in keyboard_config:
            for shortcut, action in keyboard_config["shortcuts"].items():
                # Validate shortcut format (basic check)
                if not isinstance(shortcut, str) or len(shortcut) < 2:
                    results["valid"] = False
                    results["errors"].append(
                        f"Invalid keyboard shortcut: {shortcut}"
                    )
        
        return results
    
    def validate_aria_config(self, aria_config: Dict[str, Any]) -> Dict[str, Any]:
        """Validate ARIA configuration."""
        results = {"valid": True, "errors": []}
        
        # Check required ARIA properties
        required_aria_props = ["label", "description", "role"]
        for prop in required_aria_props:
            if prop not in aria_config:
                results["valid"] = False
                results["errors"].append(
                    f"Missing ARIA property: {prop}"
                )
        
        return results
    
    def validate_responsive(self) -> Dict[str, Any]:
        """Validate responsive design configuration."""
        section_results = {"valid": True, "errors": [], "warnings": []}
        responsive = self.theme_config.get("responsive", {})
        
        if not responsive:
            section_results["valid"] = False
            section_results["errors"].append("No responsive configuration")
            return section_results
        
        # Validate breakpoints
        breakpoints = responsive.get("breakpoints", {})
        if not breakpoints:
            section_results["valid"] = False
            section_results["errors"].append("No breakpoints defined")
        
        return section_results
```

### 11. Complete Example

```python
# Complete Theme Example
theme_config = {
    "name": "Modern Tech Dashboard",
    "description": "A clean, professional theme for tech dashboards",
    "target_audience": "Data analysts and developers",
    "primary_use_case": "Data visualization dashboard",
    
    "colors": {
        "primary": "#6366f1",
        "secondary": "#8b5cf6",
        "accent": "#ec4899",
        "background": "#f9fafb",
        "card-background": "#ffffff",
        "text": "#1e293b",
        "text-secondary": "#475569",
        "text-on-primary": "#ffffff",
        "border": "#e2e8f0",
        "error": "#f87171",
        "success": "#10b981",
        "warning": "#f59e0b"
    },
    
    "typography": {
        "primary": {
            "family": "Inter",
            "weights": [400, 500, 600, 700],
            "sizes": {
                "body": "16px",
                "heading": "24px",
                "subheading": "20px"
            },
            "line_height": "1.5",
            "letter_spacing": "0.5px"
        },
        "mono": {
            "family": "Courier New",
            "weights": [400, 700],
            "sizes": {
                "body": "14px",
                "code": "16px"
            }
        }
    },
    
    "components": {
        "button": {
            "base": {
                "padding": "12px 24px",
                "border_radius": "8px",
                "transition": "all 0.3s ease"
            },
            "primary": {
                "background": "var(--primary)",
                "color": "var(--text)",
                "border": "none",
                "&:hover": {
                    "background": "var(--primary-dark)"
                }
            },
            "secondary": {
                "background": "var(--secondary)",
                "color": "var(--text)",
                "border": "none",
                "&:hover": {
                    "background": "var(--secondary-dark)"
                }
            }
        },
        "card": {
            "base": {
                "padding": "24px",
                "border_radius": "12px",
                "border": "1px solid var(--border)"
            },
            "elevated": {
                "box_shadow": "0 8px 30px rgba(0, 0, 0, 0.1)"
            }
        }
    },
    
    "accessibility": {
        "contrast": {
            "text_on_background": 4.8,
            "primary_button": 5.2
        },
        "keyboard": {
            "focus_visible": """
                outline: 2px solid var(--primary);
                outline-offset: 2px;
            """,
            "focus_ring": """
                &:focus-visible {
                    outline: 3px solid var(--primary-dark);
                    outline-offset: 3px;
                }
            """
        },
        "aria": {
            "label": "Main navigation",
            "description": "Primary site navigation",
            "role": "navigation"
        }
    },
    
    "animations": {
        "fade_in": "opacity: 0; transform: translateY(20px); animation: fadeIn 0.5s ease;",
        "slide_in": "opacity: 0; transform: translateX(-50px); animation: slideIn 0.5s ease;"
    },
    
    "responsive": {
        "breakpoints": {
            "mobile": "max-width: 768px",
            "tablet": "max-width: 1024px"
        },
        "queries": {
            "mobile": """
                .sidebar {
                    display: none !important;
                }
                
                .header {
                    padding: 24px !important;
                }
            """,
            "tablet": """
                .container {
                    grid-template-columns: 1fr 2fr !important;
                }
            """
        }
    }
}
```

### 12. Theme Builder CLI

```python
import argparse
from typing import Dict, Any, Optional
import json
from dataclasses import dataclass, field
from enum import Enum


class ThemeType(Enum):
    """Available theme types."""
    MODERN = "modern"
    CLASSIC = "classic"
    MINIMAL = "minimal"
    DARK = "dark"


@dataclass
class ThemeConfig:
    """Theme configuration data class."""
    name: str = "untitled-theme"
    description: str = "A new theme"
    author: str = "Anonymous"
    primary_color: str = "#6366f1"
    secondary_color: str = "#8b5cf6"
    background_color: str = "#f9fafb"
    text_color: str = "#1e293b"
    font_family: str = "'Inter', sans-serif"
    font_size: int = 16
    line_height: float = 1.5
    breakpoints: Dict[str, str] = field(default_factory=lambda: {
        "mobile": "768px",
        "tablet": "1024px",
        "laptop": "1440px"
    })
    
    # Additional properties
    card_shadow: str = "0 4px 6px -1px rgba(0, 0, 0, 0.1)"
    border_radius: int = 8
    transition_duration: str = "0.3s"


class ThemeBuilder:
    """
    CLI tool for building and managing themes.
    """
    
    def __init__(self):
        self.config: Optional[ThemeConfig] = None
        self.theme_type: Optional[ThemeType] = None
    
    def run(self):
        """Entry point for the CLI."""
        parser = argparse.ArgumentParser(description="Theme Builder CLI")
        parser.add_argument("command", nargs="?", help="Command to execute")
        parser.add_argument("--name", help="Theme name")
        parser.add_argument("--type", help="Theme type")
        parser.add_argument("--output", help="Output file")
        
        args = parser.parse_args()
        
        if args.command == "new" or args.command is None:
            self.create_new_theme(args)
        
        elif args.command == "build":
            self.build_theme(args)
        
        elif args.command == "preview":
            self.preview_theme(args)
        
        elif args.command == "validate":
            self.validate_theme(args)
        
        else:
            print("Unknown command. Use 'new', 'build', 'preview', or 'validate'")
    
    def create_new_theme(self, args):
        """Create a new theme configuration."""
        print("\n=== CREATE NEW THEME ===")
        
        if args.name:
            name = args.name
        else:
            name = input("Enter theme name: ")
        
        if not name:
            print("Theme name is required.")
            return
        
        print(f"\nCreating theme: {name}")
        
        # Determine theme type
        if args.type:
            self.theme_type = ThemeType(args.type)
        else:
            print("Select theme type:")
            for i, t in enumerate(ThemeType, 1):
                print(f"{i}. {t.value}")
            
            choice = input("Enter choice (1-4): ")
            
            if choice.isdigit() and 1 <= int(choice) <= len(ThemeType):
                self.theme_type = list(ThemeType)[int(choice) - 1]
            else:
                self.theme_type = ThemeType.MODERN
        
        # Generate theme configuration based on type
        self.config = self.generate_theme_config(name, self.theme_type)
        
        if args.output:
            self.save_theme(args.output)
            print(f"Theme created and saved to {args.output}")
        else:
            print("Theme configuration:")
            print(json.dumps(self.config, indent=2))
    
    def generate_theme_config(self, name: str, theme_type: ThemeType) -> ThemeConfig:
        """Generate a theme configuration based on type."""
        config = ThemeConfig()
        config.name = name
        
        # Set type-specific defaults
        if theme_type == ThemeType.MODERN:
            config.primary_color = "#6366f1"
            config.secondary_color = "#8b5cf6"
            config.background_color = "#f9fafb"
            config.text_color = "#1e293b"
            config.font_family = "'Inter', sans-serif"
        
        elif theme_type == ThemeType.CLASSIC:
            config.primary_color = "#2563eb"
            config.secondary_color = "#3b82f6"
            config.background_color = "#ffffff"
            config.text_color = "#1f2937"
            config.font_family = "'Georgia', serif"
        
        elif theme_type == ThemeType.MINIMAL:
            config.primary_color = "#3b82f6"
            config.secondary_color = "#60a5fa"
            config.background_color = "#f3f4f6"
            config.text_color = "#111827"
            config.font_family = "'Helvetica', sans-serif"
        
        elif theme_type == ThemeType.DARK:
            config.primary_color = "#6366f1"
            config.secondary_color = "#8b5cf6"
            config.background_color = "#0f172a"
            config.text_color = "#f1f5f9"
            config.font_family = "'Courier', monospace"
        
        return config
    
    def build_theme(self, args):
        """Build the theme files."""
        if not self.config:
            print("No theme configuration found. Run 'new' first.")
            return
        
        print("\n=== BUILDING THEME ===")
        
        # Generate CSS
        css_content = self.generate_css()
        
        # Generate JSON configuration
        json_content = json.dumps(self.config, indent=2)
        
        # Output files
        if args.output:
            output_dir = args.output
        else:
            output_dir = "dist"
        
        import os
        os.makedirs(output_dir, exist_ok=True)
        
        # Write files
        with open(os.path.join(output_dir, "theme.json"), "w") as json_file:
            json_file.write(json_content)
        
        with open(os.path.join(output_dir, "style.css"), "w") as css_file:
            css_file.write(css_content)
        
        print(f"Theme built successfully in {output_dir}/")
        print("Files created:")
        print("- theme.json")
        print("- style.css")
    
    def generate_css(self) -> str:
        """Generate CSS from theme configuration."""
        css = "/* Auto-generated CSS - do not edit manually */\n\n"
        
        # Variables
        css += ":root {\n"
        for key, value in self.config.__dict__.items():
            if key not in ["breakpoints", "transition_duration", "font_size"]:
                css += f"  --{key.replace('_', '-')}: {value};\n"
        css += "}\n\n"
        
        # Base styles
        css += "body {\n"
        css += f"  background-color: var(--background-color);\n"
        css += f"  color: var(--text-color);\n"
        css += f"  font-family: var(--font-family);\n"
        css += f"  font-size: var(--font-size, 16px);\n"
        css += f"  line-height: var(--line-height, 1.5);\n"
        css += "}\n\n"
        
        # Buttons
        css += "button {\n"
        css += f"  background-color: var(--primary-color);\n"
        css += f"  color: var(--text-color);\n"
        css += f"  padding: 12px 24px;\n"
        css += f"  border-radius: var(--border-radius)px;\n"
        css += f"  transition: all var(--transition-duration);\n"
        css += "}\n"
        
        return css
    
    def preview_theme(self, args):
        """Start the theme preview server."""
        if not self.config:
            print("No theme configuration found. Run 'new' first.")
            return
        
        print("\n=== STARTING PREVIEW ===")
        print("Visiting http://localhost:8000")
        
        from http.server import SimpleHTTPRequestHandler, HTTPServer
        import threading
        
        class PreviewHandler(SimpleHTTPRequestHandler):
            def do_GET(self):
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                self.wfile.write(bytes(self._get_html(), 'utf-8'))
            
            def _get_html(self) -> str:
                return """
                    <!DOCTYPE html>
                    <html lang="en">
                    <head>
                        <meta charset="UTF-8">
                        <title>Theme Preview</title>
                        <style>
                            body {
                                padding: 24px;
                                background: var(--background-color);
                                color: var(--text-color);
                                font-family: var(--font-family);
                                font-size: var(--font-size);
                                line-height: var(--line-height);
                            }
                            
                            h1 {
                                font-size: 2rem;
                                margin-bottom: 24px;
                            }
                            
                            .section {
                                margin-bottom: 36px;
                                padding: 24px;
                                background: var(--card-background);
                                border-radius: var(--border-radius)px;
                                border: 1px solid var(--border);
                            }
                            
                            button {
                                padding: 12px 24px;
                                margin-right: 8px;
                                margin-bottom: 8px;
                                background: var(--primary-color);
                                color: white;
                                border: none;
                                border-radius: 4px;
                                cursor: pointer;
                            }
                        </style>
                    </head>
                    <body>
                        <h1>Theme Preview: {name}</h1>
                        
                        <div class="section">
                            <h2>Colors</h2>
                            <div style="display: flex; gap: 24px; flex-wrap: wrap;">
                                <div style="flex: 1; min-width: 200px;">
                                    <div style="background: var(--primary-color); padding: 24px; border-radius: 8px;">Primary Color</div>
                                    <div style="color: white; padding: 24px; border-radius: 8px; background: var(--primary-color);">Text on Primary</div>
                                </div>
                                <div style="flex: 1; min-width: 200px;">
                                    <div style="background: var(--secondary-color); padding: 24px; border-radius: 8px;">Secondary Color</div>
                                    <div style="color: white; padding: 24px; border-radius: 8px; background: var(--secondary-color);">Text on Secondary</div>
                                </div>
                            </div>
                        </div>
                        
                        <div class="section">
                            <h2>Buttons</h2>
                            <button>Primary Button</button>
                            <button style="background: var(--secondary-color);">Secondary Button</button>
                        </div>
                    </body>
                    </html>
                """.format(name=self.config.name)
        
        httpd = HTTPServer(('localhost', 8000), PreviewHandler)
        print("Preview server running...")
        
        # Run in separate thread to keep CLI responsive
        server_thread = threading.Thread(target=httpd.serve_forever)
        server_thread.daemon = True
        server_thread.start()
    
    def validate_theme(self, args):
        """Validate the theme configuration."""
        if not self.config:
            print("No theme configuration found. Run 'new' first.")
            return
        
        print("\n=== VALIDATING THEME ===")
        validator = ThemeValidator(self.config)
        results = validator.validate_all()
        
        if results["overall"]["valid"]:
            print("✅ Theme is valid!")
        else:
            print("❌ Theme has issues:")
            for error in results["overall"]["errors"]:
                print(f"  - {error}")
            
            if results["overall"]["warnings"]:
                print("\n⚠️ Warnings:")
                for warning in results["overall"]["warnings"]:
                    print(f"  - {warning}")
        
        print("\nDetailed validation results:")
        print(json.dumps(results, indent=2))
    
    def save_theme(self, filename: str):
        """Save theme configuration to file."""
        with open(filename, "w") as file:
            json.dump(self.config.__dict__, file, indent=2)


if __name__ == "__main__":
    ThemeBuilder().run()
```

### 13. Project Structure

```
theme-builder/
├── theme_builder.py           # Main CLI application
├── theme_components.py        # Component definitions
├── accessibility.py           # Accessibility implementation
├── animations.py              # Animation utilities
├── breakpoints.py             # Responsive design
├── colors.py                  # Color management
├── typography.py              # Typography settings
├── validation.py              # Validation system
├── preview.py                 # Preview server
├── build.py                   # Build utilities
├── examples/                  # Example themes
│   └── modern_dashboard.py
│   └── classic_admin.py
├── dist/                      # Built output
│   ├── theme.json
│   ├── style.css
│   └── preview.html
├── tests/                     # Testing suite
│   └── test_theme.py
├── requirements.txt
└── README.md
```

### 14. Installation & Usage

```bash
# Installation
pip install .
# or
pip install git+https://github/theme-builder.git

# Usage
theme-builder new --name "My Awesome Theme" --type modern
theme-builder build --output ./my-theme
theme-builder preview
theme-builder validate
```

### 15. Advanced Configuration

```yaml
# advanced-theme-config.yaml
name: "Professional Analytics Suite"
description: "Complete analytics dashboard theme with dark mode support"

colors:
  primary: "#6366f1"
  primary-dark: "#4f46e5"
  secondary: "#8b5cf6"
  accent: "#ec4899"
  warning: "#f59e0b"
  error: "#f87171"
  success: "#10b981"
  background: "#f9fafb"
  background-dark: "#1e293b"
  card: "#ffffff"
  card-dark: "#1f2937"
  text: "#1e293b"
  text-dark: "#f1f5f9"
  text-secondary: "#475569"
  border: "#e5e7eb"
  border-dark: "#475569"

typography:
  primary:
    family: "'Inter', 'Helvetica', sans-serif"
    weights:
      - 300
      - 400
      - 500
      - 600
      - 700
    sizes:
      xs: "12px"
      sm: "14px"
      md: "16px"
      lg: "18px"
      xl: "20px"
      heading: "24px"
      subheading: "20px"
    line_height:
      default: "1.5"
      heading: "1.25"
    letter_spacing: "0.5px"
  mono:
    family: "'Courier New', monospace"
    weights:
      - 400
      - 700
    sizes:
      code: "14px"
      terminal: "16px"

components:
  button:
    base:
      padding: "12px 24px"
      border_radius: "8px"
      transition: "all 0.3s ease"
    primary:
      background: "var(--primary)"
      color: "var(--text)"
      "&:hover": 
        background: "var(--primary-dark)"
    secondary:
      background: "var(--secondary)"
      color: "var(--text)"
      "&:hover": 
        background: "var(--secondary-dark)"
    danger:
      background: "var(--error)"
      color: "white"
      "&:hover": 
        background: "var(--error-dark)"
    outline:
      background: "transparent"
      color: "var(--primary)"
      border: "2px solid var(--primary)"
      "&:hover": 
        background: "var(--primary)"
  input:
    base:
      padding: "12px 16px"
      border: "1px solid var(--border)"
      border_radius: "6px"
      "&:focus": 
        outline: "none"
        border_color: "var(--primary)"
    danger:
      border_color: "var(--error)"
      "&:focus": 
        border_color: "var(--error-dark)"
  card:
    base:
      padding: "24px"
      border_radius: "12px"
      border: "1px solid var(--border)"
    elevated:
      box_shadow: "0 8px 30px rgba(0, 0, 0, 0.1)"
    dark:
      background: "var(--card-dark)"
      border: "1px solid var(--border-dark)"

animations:
  fade_in: "opacity: 0; transform: translateY(20px); animation: fadeIn 0.5s ease;"
  slide_in: "opacity: 0; transform: translateX(-50px); animation: slideIn 0.5s ease;"
  bounce_in: "opacity: 0; transform: scale(0.5); animation: bounceIn 0.6s ease;"

accessibility:
  contrast:
    text_on_background: 4.8
    primary_button: 5.2
    secondary_button: 4.5
  keyboard:
    focus_visible: 
      outline: "2px solid var(--primary)"
      outline_offset: "2px"
    focus_ring: 
      "&:focus-visible": 
        outline: "3px solid var(--primary-dark)"
        outline_offset: "3px"
  aria: 
    label: "Main content area"
    description: "Primary content container"
    role: "main"

responsive:
  breakpoints:
    mobile: "768px"
    tablet: "1024px"
    laptop: "1440px"
    desktop: "1920px"
  queries:
    mobile:
      ".sidebar": "display: none !important;"
      ".header": "padding: 24px !important;"
    tablet:
      ".container": "grid-template-columns: 1fr 2fr !important;"
    laptop:
      ".chart-container": "max-width: 800px !important;"
```

### 16. API Reference

```python
"""
Theme Builder API Reference
=========================

This documentation provides reference information for the Theme Builder API.
"""

from typing import Dict, List, Optional, Any, Callable
from enum import Enum
import json


class ConfigKeys(Enum):
    """Configuration keys available in theme configuration."""
    
    # Basic theme information
    NAME = "name"
    DESCRIPTION = "description"
    AUTHOR = "author"
    VERSION = "version"
    
    # Color configuration
    COLORS = "colors"
    PRIMARY_COLOR = "primary_color"
    SECONDARY_COLOR = "secondary_color"
    BACKGROUND_COLOR = "background_color"
    TEXT_COLOR = "text_color"
    ACCENT_COLOR = "accent_color"
    
    # Typography configuration
    TYPOGRAPHY = "typography"
    FONT_FAMILY = "font_family"
    FONT_SIZE = "font_size"
    LINE_HEIGHT = "line_height"
    LETTER_SPACING = "letter_spacing"
    
    # Component styles
    COMPONENTS = "components"
    BUTTON = "button"
    INPUT = "input"
    CARD = "card"
    
    # Accessibility settings
    ACCESSIBILITY = "accessibility"
    CONTRAST = "contrast"
    KEYBOARD = "keyboard"
    ARIA = "aria"
    
    # Responsive design
    RESPONSIVE = "responsive"
    BREAKPOINTS = "breakpoints"
    QUERIES = "queries"
    
    # Animations
    ANIMATIONS = "animations"


class ConfigValidationErrors(Exception):
    """Custom exception for configuration validation errors."""
    def __init__(self, errors: List[Dict[str, Any]]):
        self.errors = errors
        super().__init__(f"Configuration validation failed: {len(errors)} errors")


class ThemeValidator:
    """
    Validates theme configuration against predefined schema.
    """
    
    @staticmethod
    def validate(config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Validate the entire theme configuration.
        
        Args:
            config: Theme configuration dictionary
            
        Returns:
            Validation results dictionary
            
        Raises:
            ConfigValidationErrors: If validation fails
        """
        results = {
            "valid": True,
            "errors": [],
            "warnings": []
        }
        
        # Validate required top-level keys
        required_top_level = [ConfigKeys.NAME.value, ConfigKeys.COLORS.value]
        for key in required_top_level:
            if key not in config:
                results["errors"].append({
                    "level": "error",
                    "message": f"Missing required top-level key: {key}"
                })
                results["valid"] = False
        
        # Validate colors section
        ThemeValidator._validate_colors(config.get(ConfigKeys.COLORS.value, {}), results)
        
        # Validate typography section
        ThemeValidator._validate_typography(config.get(ConfigKeys.TYPOGRAPHY.value, {}), results)
        
        # Validate accessibility
        ThemeValidator._validate_accessibility(config.get(ConfigKeys.ACCESSIBILITY.value, {}), results)
        
        # Validate responsive design
        ThemeValidator._validate_responsive(config.get(ConfigKeys.RESPONSIVE.value, {}), results)
        
        if not results["valid"]:
            raise ConfigValidationErrors(results["errors"])
        
        return results
    
    @staticmethod
    def _validate_colors(colors: Dict[str, Any], results: Dict[str, Any]):
        """Validate colors configuration."""
        required_colors = ["primary", "text", "background"]
        for color in required_colors:
            if color not in colors:
                results["errors"].append({
                    "level": "error",
                    "message": f"Missing required color: {color}"
                })
                results["valid"] = False
        
        # Check color contrast ratios
        try:
            from colorcontrast import contrast_ratio
            
            primary = colors.get("primary", "#000000")
            text = colors.get("text", "#ffffff")
            ratio = contrast_ratio(primary, text)
            
            if ratio < 4.5:
                results["errors"].append({
                    "level": "error",
                    "message": f"Contrast ratio between primary and text colors ({ratio:.2f}) is below AA standard (4.5:1)"
                })
                results["valid"] = False
            
        except ImportError:
            # Skip contrast validation if colorcontrast not installed
            pass
    
    @staticmethod
    def _validate_typography(typography: Dict[str, Any], results: Dict[str, Any]):
        """Validate typography configuration."""
        if not typography:
            results["warnings"].append({
                "level": "warning",
                "message": "Typography configuration not found"
            })
            return
        
        required_typography = ["font_family", "font_size"]
        for key in required_typography:
            if key not in typography:
                results["errors"].append({
                    "level": "error",
                    "message": f"Missing required typography property: {key}"
                })
                results["valid"] = False
    
    @staticmethod
    def _validate_accessibility(accessibility: Dict[str, Any], results: Dict[str, Any]):
        """Validate accessibility configuration."""
        if not accessibility:
            results["warnings"].append({
                "level": "warning",
                "message": "Accessibility configuration not found"
            })
            return
        
        required_accessibility = ["contrast"]
        for key in required_accessibility:
            if key not in accessibility:
                results["errors"].append({
                    "level": "error",
                    "message": f"Missing required accessibility property: {key}"
                })
                results["valid"] = False
    
    @staticmethod
    def _validate_responsive(responsive: Dict[str, Any], results: Dict[str, Any]):
        """Validate responsive design configuration."""
        if not responsive:
            results["warnings"].append({
                "level": "warning",
                "message": "Responsive configuration not found"
            })
            return
        
        required_breakpoints = ["mobile", "tablet"]
        for bp in required_breakpoints:
            if bp not in responsive.get("breakpoints", {}):
                results["warnings"].append({
                    "level": "warning",
                    "message": f"Missing breakpoint: {bp}"
                })


class ThemeExporter:
    """
    Handles exporting theme configurations to various formats.
    """
    
    @staticmethod
    def to_json(config: Dict[str, Any], pretty: bool = True) -> str:
        """
        Export theme configuration to JSON.
        
        Args:
            config: Theme configuration dictionary
            pretty: Whether to use pretty-printing
            
        Returns:
            JSON string
        """
        return json.dumps(config, indent=2 if pretty else None)
    
    @staticmethod
    def to_css(config: Dict[str, Any]) -> str:
        """
        Generate CSS from theme configuration.
        
        Args:
            config: Theme configuration dictionary
            
        Returns:
            CSS string
        """
        css = "/* Auto-generated CSS from theme configuration */\n\n"
        
        # Variable definitions
        css += ":root {\n"
        for key, value in config.get("colors", {}).items():
            css += f"  --{key}: {value};\n"
        css += "}\n\n"
        
        # Base styles
        css += "body {\n"
        css += f"  font-family: {config.get('typography', {}).get('font_family', 'sans-serif')};\n"
        css += f"  font-size: {config.get('typography', {}).get('font_size', '16px')};\n"
        css += f"  background-color: var(--background);\n"
        css += "}\n"
        
        return css
    
    @staticmethod
    def to_scss(config: Dict[str, Any]) -> str:
        """
        Generate SCSS from theme configuration.
        
        Args:
            config: Theme configuration dictionary
            
        Returns:
            SCSS string
        """
        scss = "/* Auto-generated SCSS from theme configuration */\n\n"
        
        # Variable definitions
        scss += "@use 'sass:map';\n\n"
        scss += "$theme: (;\n"
        
        for key, value in config.get("colors", {}).items():
            scss += f"  '{key}': #{value.replace('#', '')},\n"
        
        scss += ");\n\n"
        
        # Mixin for button styles
        scss += "@mixin button-style($variant) {\n"
        scss += "  @include variant($variant);\n"
        scss += "}\n"
        
        return scss


class ThemeImporter:
    """
    Handles importing theme configurations from various formats.
    """
    
    @staticmethod
    def from_json(data: str) -> Dict[str, Any]:
        """
        Import theme configuration from JSON.
        
        Args:
            data: JSON string
            
        Returns:
            Theme configuration dictionary
        """
        return json.loads(data)
    
    @staticmethod
    def from_css(css: str) -> Dict[str, Any]:
        """
        Parse CSS and extract theme configuration.
        
        Args:
            css: CSS string
            
        Returns:
            Extracted theme configuration
        """
        config = {}
        colors = {}
        
        # Simple parser - this is a basic implementation
        for line in css.split("\n"):
            if line.startswith("--"):
                # CSS variable
                parts = line.split(":")
                if len(parts) >= 2:
                    key = parts[0].strip().replace("--", "", 1).strip()
                    value = parts[1].strip()
                    colors[key] = value
        
        if colors:
            config["colors"] = colors
        
        return config


class ThemeTransformer:
    """
    Provides utilities for transforming and modifying themes.
    """
    
    @staticmethod
    def transform_config(
        config: Dict[str, Any],
        transformer: Callable[[Dict[str, Any]], Dict[str, Any]]
    ) -> Dict[str, Any]:
        """
        Transform theme configuration using a custom transformer function.
        
        Args:
            config: Original theme configuration
            transformer: Function that modifies the configuration
            
        Returns:
            Transformed theme configuration
        """
        return transformer(config)
    
    @staticmethod
    def convert_to_dark_mode(config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Convert a light mode theme to dark mode.
        
        Args:
            config: Light mode theme configuration
            
        Returns:
            Dark mode theme configuration
        """
        transformed = config.copy()
        
        # Flip colors
        colors = transformed.get("colors", {})
        if colors:
            transformed["colors"] = {
                "primary": colors.get("primary", "#6366f1"),
                "secondary": colors.get("secondary", "#8b5cf6"),
                "background": colors.get("background-dark", "#1e293b"),
                "text": colors.get("text-dark", "#f1f5f9"),
                "card": colors.get("card-dark", "#1f2937"),
                "border": colors.get("border-dark", "#475569")
            }
        
        # Adjust typography for better readability
        if "typography" in transformed:
            transformed["typography"]["text_color"] = colors.get("text", "#000000")
        
        return transformed
    
    @staticmethod
    def convert_to_light_mode(config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Convert a dark mode theme to light mode.
        
        Args:
            config: Dark mode theme configuration
            
        Returns:
            Light mode theme configuration
        """
        transformed = config.copy()
        
        # Flip colors back
        colors = transformed.get("colors", {})
        if colors:
            transformed["colors"] = {
                "primary": colors.get("primary", "#6366f1"),
                "secondary": colors.get("secondary", "#8b5cf6"),
                "background": colors.get("background-light", "#f9fafb"),
                "text": colors.get("text-light", "#1e293b"),
                "card": colors.get("card-light", "#ffffff"),
                "border": colors.get("border-light", "#e5e7eb")
            }
        
        # Adjust typography
        if "typography" in transformed:
            transformed["typography"]["text_color"] = colors.get("text", "#000000")
        
        return transformed
```

### 17. Advanced Features

```python
class ThemeManager:
    """
    Manages multiple themes and theme switching.
    """
    
    def __init__(self):
        self.themes: Dict[str, Dict[str, Any]] = {}
        self.current_theme: Optional[str] = None
    
    def load_theme(self, name: str, config: Dict[str, Any]):
        """
        Load a new theme into the manager.
        
        Args:
            name: Theme name
            config: Theme configuration dictionary
        """
        # Validate theme first
        validator = ThemeValidator()
        validator.validate(config)
        
        self.themes[name] = config
    
    def set_current_theme(self, name: str):
        """
        Set the current active theme.
        
        Args:
            name: Theme name
        """
        if name in self.themes:
            self.current_theme = name
            self.apply_theme()
        else:
            print(f"Theme '{name}' not found")
    
    def apply_theme(self):
        """Apply the current theme to the application."""
        if self.current_theme and self.themes[self.current_theme]:
            # In a real application, you would update the DOM and styles
            print(f"Applying theme: {self.current_theme}")
            # This is a simplified demonstration
            self._update_css_variables(self.themes[self.current_theme])
    
    def _update_css_variables(self, theme: Dict[str, Any]):
        """Update CSS variables with theme colors."""
        document = self._get_document()
        if not document:
            return
        
        # Clear existing variables
        document.documentElement.style.setProperty('--primary', '')
        document.documentElement.style.setProperty('--text', '')
        document.documentElement.style.setProperty('--background', '')
        
        # Set new variables
        document.documentElement.style.setProperty(
            '--primary', 
            theme.get('colors', {}).get('primary', '#6366f1')
        )
        document.documentElement.style.setProperty(
            '--text', 
            theme.get('colors', {}).get('text', '#1e293b')
        )
        document.documentElement.style.setProperty(
            '--background', 
            theme.get('colors', {}).get('background', '#f9fafb')
        )
    
    def _get_document(self):
        """Helper to get the document object - this would be different in a framework."""
        try:
            from browser import document
            return document
        except ImportError:
            return None
    
    def list_themes(self):
        """List all loaded themes."""
        if not self.themes:
            print("No themes loaded.")
            return
        
        print("Loaded themes:")
        for name in self.themes:
            print(f"- {name}")
    
    def export_all_themes(self, output_dir: str):
        """
        Export all themes to disk.
        
        Args:
            output_dir: Directory to save theme files
        """
        import os
        os.makedirs(output_dir, exist_ok=True)
        
        for name, theme in self.themes.items():
            file_path = os.path.join(output_dir, f"{name}.json")
            with open(file_path, 'w') as file:
                json.dump(theme, file, indent=2)
            print(f"Exported theme: {file_path}")
    
    def import_themes_from_directory(self, input_dir: str):
        """
        Import all themes from a directory.
        
        Args:
            input_dir: Directory containing theme files
        """
        import os
        
        for filename in os.listdir(input_dir):
            if filename.endswith('.json'):
                file_path = os.path.join(input_dir, filename)
                with open(file_path, 'r') as file:
                    theme_data = json.load(file)
                
                theme_name = os.path.splitext(filename)[0]
                self.load_theme(theme_name, theme_data)
                print(f"Imported theme: {theme_name}")


# Example usage
if __name__ == "__main__":
    manager = ThemeManager()
    
    # Load some themes
    light_theme = {
        "name": "Light Theme",
        "colors": {
            "primary": "#6366f1",
            "secondary": "#8b5cf6",
            "background": "#f9fafb",
            "text": "#1e293b"
        }
    }
    
    dark_theme = {
        "name": "Dark Theme",
        "colors": {
            "primary": "#6366f1",
            "secondary": "#8b5cf6",
            "background": "#0f172a",
            "text": "#f1f5f9"
        }
    }
    
    manager.load_theme("light", light_theme)
    manager.load_theme("dark", dark_theme)
    
    # Set current theme
    manager.set_current_theme("dark")
    
    # List themes
    manager.list_themes()
    
    # Export themes
    manager.export_all_themes("./exported_themes")
```

### 18. Performance Optimization

```python
class ThemeOptimizer:
    """
    Optimizes theme configurations for performance.
    """
    
    @staticmethod
    def minify_css(css: str) -> str:
        """
        Minify CSS content.
        
        Args:
            css: Original CSS
            
        Returns:
            Minified CSS
        """
        # Remove comments
        css = "\n".join([line for line in css.split("\n") if not line.strip().startswith(("/*", "*"))])
        
        # Remove whitespace
        css = " ".join(css.split())
        
        return css
    
    @staticmethod
    def optimize_json(json_data: Dict[str, Any]) -> Dict[str, Any]:
        """
        Optimize JSON configuration for performance.
        
        Args:
            json_data: Theme configuration
            
        Returns:
            Optimized JSON configuration
        """
        optimized = {}
        
        # Only keep essential keys
        essential_keys = ["colors", "typography", "components"]
        
        for key in essential_keys:
            if key in json_data:
                optimized[key] = json_data[key]
        
        # Convert lists to tuples for faster access
        for key, value in optimized.items():
            if isinstance(value, dict):
                optimized[key] = ThemeOptimizer.optimize_json(value)
            elif isinstance(value, list):
                optimized[key] = tuple(value)
        
        return optimized
    
    @staticmethod
    def lazy_load_theme(theme: Dict[str, Any]) -> Dict[str, Any]:
        """
        Create a lazily loaded version of the theme.
        
        Args:
            theme: Full theme configuration
            
        Returns:
            Lazily loaded theme with only essential data
        """
        return {
            "colors": theme.get("colors", {}),
            "typography": theme.get("typography", {}),
            "components": {
                "button": theme.get("components", {}).get("button", {})
            }
        }
    
    @staticmethod
    def cache_theme(theme: Dict[str, Any]) -> Dict[str, Any]:
        """
        Cache theme configuration for faster access.
        
        Args:
            theme: Theme configuration
            
        Returns:
            Cached theme configuration
        """
        from functools import lru_cache
        
        @lru_cache(maxsize=128)
        def get_property(key: str) -> Any:
            return theme.get(key)
        
        return {
            "get_property": get_property,
            "meta": {
                "size": len(json.dumps(theme)),
                "keys": len(theme.keys())
            }
        }
    
    @staticmethod
    def generate_theme_hash(theme: Dict[str, Any]) -> str:
        """
        Generate a hash for the theme configuration.
        This helps with caching and versioning.
        
        Args:
            theme: Theme configuration
            
        Returns:
            SHA-1 hash of the theme
        """
        import hashlib
        import json
        
        theme_json = json.dumps(theme, sort_keys=True)
        hash_object = hashlib.sha1(theme_json.encode())
        return hash_object.hexdigest()[:8]
    
    @staticmethod
    def split_theme_into_chunks(
        theme: Dict[str, Any],
        chunk_size: int = 5
    ) -> List[Dict[str, Any]]:
        """
        Split a large theme into smaller chunks for lazy loading.
        
        Args:
            theme: Full theme configuration
            chunk_size: Maximum number of properties per chunk
            
        Returns:
            List of theme chunks
        """
        properties = {}
        
        # Separate properties by type
        for key, value in theme.items():
            if key.startswith(("colors", "typography", "components")):
                properties[key] = value
        
        # Split into chunks
        chunks = []
        current_chunk = {}
        count = 0
        
        for key, value in properties.items():
            current_chunk[key] = value
            count += 1
            if count >= chunk_size:
                chunks.append(current_chunk)
                current_chunk = {}
                count = 0
        
        if current_chunk:
            chunks.append(current_chunk)
        
        return chunks
```

### 19. Internationalization

```python
class ThemeTranslator:
    """
    Handles localization and internationalization for themes.
    """
    
    SUPPORTED_LANGUAGES = ["en", "es", "fr", "de", "ja", "ko", "zh"]
    
    def __init__(self):
        self.translations: Dict[str, Dict[str, str]] = {
            "en": {
                "common": {
                    "save": "Save",
                    "cancel": "Cancel",
                    "apply": "Apply",
                    "close": "Close",
                    "ok": "OK",
                    "delete": "Delete",
                    "edit": "Edit",
                    "add": "Add",
                    "remove": "Remove"
                },
                "theme_editor": {
                    "title": "Theme Editor",
                    "name_label": "Theme Name",
                    "description_label": "Description",
                    "colors_tab": "Colors",
                    "typography_tab": "Typography",
                    "components_tab": "Components",
                    "preview_button": "Preview",
                    "save_button": "Save Theme"
                }
            },
            "es": {
                "common": {
                    "save": "Guardar",
                    "cancel": "Cancelar",
                    "apply": "Aplicar",
                    "close": "Cerrar",
                    "ok": "Aceptar",
                    "delete": "Eliminar",
                    "edit": "Editar",
                    "add": "Añadir",
                    "remove": "Eliminar"
                },
                "theme_editor": {
                    "title": "Editor de Temas",
                    "name_label": "Nombre del Tema",
                    "description_label": "Descripción",
                    "colors_tab": "Colores",
                    "typography_tab": "Tipografía",
                    "components_tab": "Componentes",
                    "preview_button": "Previsualizar",
                    "save_button": "Guardar Tema"
                }
            },
            # Additional languages...
        }
    
    def translate(self, key: str, language: str = "en") -> str:
        """
        Translate a key to the specified language.
        
        Args:
            key: Translation key (e.g., "theme_editor.title")
            language: Target language code
            
        Returns:
            Translated string or original key if translation not found
        """
        parts = key.split(".")
        current = self.translations.get(language, {})
        
        for part in parts:
            current = current.get(part, {})
        
        return current.get(parts[-1], key)
    
    def get_language_from_browser(self) -> str:
        """Detect user's preferred language from browser settings."""
        import locale
        
        try:
            # Try to get locale from environment
            lang = locale.getdefaultlocale()[0]
            if lang and lang.split("_")[0] in self.SUPPORTED_LANGUAGES:
                return lang.split("_")[0]
        except:
            pass
        
        # Default to English
        return "en"
    
    def load_translations(self, language: str):
        """Load translations for a specific language."""
        if language in self.SUPPORTED_LANGUAGES and language != "en":
            # In a real application, you would load this from a file
            self.translations[language] = {
                "common": {
                    "save": "Speichern",
                    "cancel": "Abbrechen",
                    "apply": "Anwenden",
                    "close": "Schließen",
                    "ok": "OK",
                    "delete": "Löschen",
                    "edit": "Bearbeiten",
                    "add": "Hinzufügen",
                    "remove": "Entfernen"
                },
                "theme_editor": {
                    "title": "Themen-Editor",
                    "name_label": "Themenname",
                    "description_label": "Beschreibung",
                    "colors_tab": "Farben",
                    "typography_tab": "Schriftarten",
                    "components_tab": "Komponenten",
                    "preview_button": "Vorschau",
                    "save_button": "Thema speichern"
                }
            }
    
    def format_number(self, number: float, language: str = "en") -> str:
        """
        Format numbers according to locale conventions.
        
        Args:
            number: Number to format
            language: Language code
            
        Returns:
            Formatted number string
        """
        if language == "en":
            return f"{number:,.2f}"
        elif language == "de":
            return f"{number:,.2f}".replace(",", ".")
        # Add more number formats as needed
        return f"{number:,.2f}"
    
    def format_date(self, date: str, language: str = "en") -> str:
        """
        Format dates according to locale conventions.
        
        Args:
            date: Date string (ISO format)
            language: Language code
            
        Returns:
            Formatted date string
        """
        from datetime import datetime
        
        d = datetime.fromisoformat(date)
        
        if language == "en":
            return d.strftime("%m/%d/%Y")
        elif language == "es":
            return d.strftime("%d/%m/%Y")
        elif language == "de":
            return d.strftime("%d.%m.%Y")
        # Additional formats...
        return d.strftime("%m/%d/%Y")


# Example usage
if __name__ == "__main__":
    translator = ThemeTranslator()
    
    # Get translations
    print(translator.translate("theme_editor.title", "es"))  # "Editor de Temas"
    print(translator.translate("common.save", "de"))         # "Speichern"
    
    # Number formatting
    print(translator.format_number(1234.56, "en"))  # "1,234.56"
    print(translator.format_number(1234.56, "de"))  # "1.234,56"
    
    # Date formatting
    print(translator.format_date("2023-10-15", "en"))  # "10/15/2023"
    print(translator.format_date("2023-10-15", "es"))  # "15/10/2023"
    print(translator.format_date("2023-10-15", "de"))  # "15.10.2023"
```

### 20. Complete Build System

```python
import os
import shutil
import json
from typing import Dict, Any, Optional, List
from dataclasses import dataclass, field
from enum import Enum
import subprocess


class BuildTarget(Enum):
    """Build target environments."""
    DEVELOPMENT = "dev"
    PRODUCTION = "prod"


@dataclass
class BuildConfig:
    """Configuration for the build process."""
    input_dir: str = "src"
    output_dir: str = "dist"
    theme_name: str = "default-theme"
    target: BuildTarget = BuildTarget.PRODUCTION
    minify: bool = True
    sourcemaps: bool = False


class Builder:
    """
    Comprehensive build system for theme projects.
    Handles compilation, optimization, and packaging.
    """
    
    def __init__(self, config: Optional[BuildConfig] = None):
        self.config = config or BuildConfig()
        self.build_id: Optional[str] = None
        self.timestamp: Optional[str] = None
    
    def prepare_build(self):
        """Prepare for a new build."""
        self.timestamp = self._get_timestamp()
        self.build_id = self._generate_build_id()
        
        print(f"Preparing build [{self.build_id}]...")
        print(f"Time: {self.timestamp}")
        
        # Create output directory
        os.makedirs(self.config.output_dir, exist_ok=True)
        
        # Create build-specific directories
        os.makedirs(os.path.join(self.config.output_dir, "themes"), exist_ok=True)
        os.makedirs(os.path.join(self.config.output_dir, "assets"), exist_ok=True)
        os.makedirs(os.path.join(self.config.output_dir, "tmp"), exist_ok=True
    
    def _get_timestamp(self) -> str:
        """Get current timestamp for build identification."""
        from datetime import datetime
        return datetime.now().isoformat(" ", "seconds")
    
    def _generate_build_id(self) -> str:
        """Generate a unique build ID."""
        import uuid
        return str(uuid.uuid4())[:8]
    
    def compile_theme(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Compile a theme configuration.
        
        Args:
            theme_config: Raw theme configuration
            
        Returns:
            Compiled theme configuration
        """
        print(f"Compiling theme: {theme_config.get('name', 'untitled')}...")
        
        # Validate theme
        validator = ThemeValidator()
        validation_results = validator.validate(theme_config)
        
        if not validation_results.get("valid", True):
            print("Skipping compilation due to validation errors.")
            return {}
        
        # Optimize theme configuration
        optimized = {
            "metadata": {
                "name": theme_config.get("name"),
                "description": theme_config.get("description"),
                "version": theme_config.get("version", "1.0.0"),
                "build_id": self.build_id,
                "timestamp": self.timestamp
            },
            "colors": self._compile_colors(theme_config.get("colors", {})),
            "typography": self._compile_typography(theme_config.get("typography", {})),
            "components": self._compile_components(theme_config.get("components", {})),
            "accessibility": self._compile_accessibility(theme_config.get("accessibility", {})),
            "responsive": self._compile_responsive(theme_config.get("responsive", {})),
            "animations": self._compile_animations(theme_config.get("animations", {}))
        }
        
        # Add validation results
        optimized["validation"] = {
            "valid": validation_results.get("valid", True),
            "warnings": validation_results.get("warnings", []),
            "errors": validation_results.get("errors", [])
        }
        
        return optimized
    
    def _compile_colors(self, colors: Dict[str, Any]) -> Dict[str, Any]:
        """Compile colors section."""
        compiled = {}
        
        for key, value in colors.items():
            if isinstance(value, str) and value.startswith(("var(", "#", "rgb(", "hsl(")):
                compiled[key] = value
            else:
                # Convert to CSS variable format if it's a simple value
                compiled[key] = f"var(--{key.lower()})", value
        
        return compiled
    
    def _compile_typography(self, typography: Dict[str, Any]) -> Dict[str, Any]:
        """Compile typography section."""
        compiled = {
            "font": {
                "family": typography.get("font_family", "sans-serif"),
                "size": typography.get("font_size", "16px"),
                "line_height": typography.get("line_height", "1.5"),
                "letter_spacing": typography.get("letter_spacing", "normal")
            }
        }
        
        # Include variant-specific styles
        if "variants" in typography:
            compiled["variants"] = typography["variants"]
        
        return compiled
    
    def _compile_components(self, components: Dict[str, Any]) -> Dict[str, Any]:
        """Compile components section."""
        compiled = {}
        
        for component, config in components.items():
            compiled[component] = {
                "base": self._compile_base_styles(config.get("base", {})),
                "variants": self._compile_variants(config.get("variants", {}))
            }
        
        return compiled
    
    def _compile_base_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Compile base styles for a component."""
        return {k: v for k, v in styles.items() if v is not None}
    
    def _compile_variants(self, variants: Dict[str, Any]) -> Dict[str, Any]:
        """Compile variant styles for a component."""
        compiled = {}
        
        for variant, config in variants.items():
            compiled[variant] = {
                "modifier": config.get("modifier"),
                "styles": self._compile_styles(config.get("styles", {}))
            }
        
        return compiled
    
    def _compile_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Compile individual styles."""
        return {k: v for k, v in styles.items() if v is not None}
    
    def _compile_accessibility(self, accessibility: Dict[str, Any]) -> Dict[str, Any]:
        """Compile accessibility settings."""
        return {
            "contrast": accessibility.get("contrast", {}),
            "keyboard": accessibility.get("keyboard", {}),
            "aria": accessibility.get("aria", {})
        }
    
    def _compile_responsive(self, responsive: Dict[str, Any]) -> Dict[str, Any]:
        """Compile responsive design settings."""
        return {
            "breakpoints": self._compile_breakpoints(responsive.get("breakpoints", {})),
            "queries": responsive.get("queries", {})
        }
    
    def _compile_breakpoints(self, breakpoints: Dict[str, Any]) -> Dict[str, Any]:
        """Compile breakpoint definitions."""
        compiled = {}
        
        for name, value in breakpoints.items():
            if isinstance(value, str):
                # Parse breakpoint string (e.g., "768px", "min-width: 1024px")
                if value.startswith(("min-width:", "max-width:")):
                    compiled[name] = value
                else:
                    compiled[name] = f"min-width: {value}"
            # Add more breakpoint parsing logic as needed
        
        return compiled
    
    def _compile_animations(self, animations: Dict[str, Any]) -> Dict[str, Any]:
        """Compile animations section."""
        compiled = {}
        
        for name, keyframes in animations.items():
            compiled[name] = {
                "keyframes": self._format_keyframes(keyframes),
                "duration": keyframes.get("duration", "0.5s"),
                "timing_function": keyframes.get("timing_function", "ease"),
                "iteration_count": keyframes.get("iteration_count", "1"),
                "direction": keyframes.get("direction", "normal")
            }
        
        return compiled
    
    def _format_keyframes(self, keyframes: Dict[str, Any]) -> Dict[str, Any]:
        """Format animation keyframes."""
        formatted = {}
        
        for selector, styles in keyframes.items():
            formatted[selector] = {
                "properties": {k: v for k, v in styles.items() if k != "selector"},
                "easing": styles.get("easing")
            }
        
        return formatted
    
    def build_all(self):
        """Build all themes in the project."""
        self.prepare_build()
        
        # Collect all theme configuration files
        theme_files = self._find_theme_files()
        
        if not theme_files:
            print("No theme files found to build.")
            return
        
        build_results = {
            "summary": {
                "themes_processed": 0,
                "themes_skipped": 0,
                "errors": 0
            },
            "details": []
        }
        
        for file_path in theme_files:
            try:
                self._build_single_theme(file_path, build_results)
            except Exception as e:
                print(f"Error building {file_path}: {str(e)}")
                build_results["summary"]["errors"] += 1
        
        self._generate_build_summary(build_results)
    
    def _find_theme_files(self) -> List[str]:
        """Find all theme configuration files in the input directory."""
        theme_files = []
        
        input_path = self.config.input_dir
        if not os.path.exists(input_path):
            print(f"Input directory not found: {input_path}")
            return theme_files
        
        for root, dirs, files in os.walk(input_path):
            for file in files:
                if file.endswith(".json"):
                    theme_files.append(os.path.join(root, file))
        
        return theme_files
    
    def _build_single_theme(self, file_path: str, results: Dict[str, Any]):
        """
        Build a single theme file.
        
        Args:
            file_path: Path to theme file
            results: Build results dictionary
        """
        print(f"\nProcessing theme: {file_path}")
        
        # Load theme configuration
        with open(file_path, "r") as file:
            theme_config = json.load(file)
        
        # Compile theme
        compiled_theme = self.compile_theme(theme_config)
        
        if not compiled_theme:
            print(f"Skipping {file_path} - no valid configuration found.")
            results["summary"]["themes_skipped"] += 1
            return
        
        # Generate output file path
        relative_path = os.path.relpath(file_path, self.config.input_dir)
        base_name = os.path.splitext(os.path.basename(file_path))[0]
        output_path = os.path.join(
            self.config.output_dir,
            "themes",
            f"{base_name}-{self.build_id}.json"
        )
        
        # Save compiled theme
        with open(output_path, "w") as file:
            json.dump(compiled_theme, file, indent=2)
        
        # Build associated CSS
        self.build_theme_css(theme_config, base_name)
        
        results["summary"]["themes_processed"] += 1
        results["details"].append({
            "file": file_path,
            "output": output_path,
            "status": "success",
            "compiled_size": len(json.dumps(compiled_theme))
        })
        
        print(f"✓ Built {file_path}")
        print(f"  → Output: {output_path}")
        print(f"  → Size: {len(json.dumps(compiled_theme))} bytes")
    
    def build_theme_css(self, theme_config: Dict[str, Any], theme_name: str):
        """
        Build CSS file for a theme.
        
        Args:
            theme_config: Theme configuration
            theme_name: Theme name for file naming
        """
        css_content = self._generate_css_from_config(theme_config)
        
        if self.config.minify:
            css_content = self._minify_css(css_content)
        
        css_output_path = os.path.join(
            self.config.output_dir,
            "assets",
            f"{theme_name}-{self.build_id}.css"
        )
        
        with open(css_output_path, "w") as css_file:
            css_file.write(css_content)
        
        print(f"  → CSS built: {css_output_path}")
    
    def _generate_css_from_config(self, theme_config: Dict[str, Any]) -> str:
        """Generate CSS from theme configuration."""
        css = "/* Generated CSS - do not edit manually */\n\n"
        
        # CSS Variables
        css += ":root {\n"
        for key, value in theme_config.get("colors", {}).items():
            css += f"  --{key.lower()}: {value};\n"
        css += "}\n\n"
        
        # Base styles
        css += "body {\n"
        css += f"  font-family: {theme_config.get('typography', {}).get('font_family', 'sans-serif')};\n"
        css += f"  font-size: {theme_config.get('typography', {}).get('font_size', '16px')};\n"
        css += f"  background-color: var(--background);\n"
        css += "}\n\n"
        
        # Button styles
        if "components" in theme_config and "button" in theme_config["components"]:
            button_config = theme_config["components"]["button"]
            css += "button {\n"
            css += f"  background-color: var(--primary);\n"
            css += f"  color: var(--text);\n"
            css += f"  padding: {button_config.get('padding', '12px 24px')};\n"
            css += f"  border-radius: {button_config.get('border_radius', '8px')};\n"
            css += "}\n\n"
        
        return css
    
    def _minify_css(self, css: str) -> str:
        """Minify CSS content."""
        # Remove comments
        css = "\n".join([line for line in css.split("\n") if not line.strip().startswith(("/*", "*"))])
        
        # Remove whitespace
        css = " ".join(css.split())
        
        return css
    
    def _generate_build_summary(self, results: Dict[str, Any]):
        """Generate a build summary report."""
        summary_path = os.path.join(
            self.config.output_dir,
            "build-summary.json"
        )
        
        with open(summary_path, "w") as summary_file:
            json.dump(results, summary_file, indent=2)
        
        print(f"\nBuild Summary:")
        print(f"══════════════════════════════════════════════════════")
        print(f"Build ID: {self.build_id}")
        print(f"Timestamp: {self.timestamp}")
        print("")
        print(f"Themes Processed: {results['summary']['themes_processed']}")
        print(f"Themes Skipped: {results['summary']['themes_skipped']}")
        print(f"Errors: {results['summary']['errors']}")
        print("")
        print(f"Total Output Size: {self._calculate_total_size()} bytes")
        print(f"Output Directory: {self.config.output_dir}")
        print("══════════════════════════════════════════════════════")
    
    def _calculate_total_size(self) -> int:
        """Calculate the total size of all build outputs."""
        total_size = 0
        output_path = self.config.output_dir
        
        for root, dirs, files in os.walk(output_path):
            for file in files:
                file_path = os.path.join(root, file)
                total_size += os.path.getsize(file_path)
        
        return total_size
    
    def clean(self):
        """Clean the build directory."""
        print(f"Cleaning build directory: {self.config.output_dir}")
        if os.path.exists(self.config.output_dir):
            shutil.rmtree(self.config.output_dir)
        os.makedirs(self.config.output_dir, exist_ok=True)
    
    def watch(self):
        """Watch for changes and rebuild automatically."""
        print("Starting watch mode...")
        print("Press Ctrl+C to exit.")
        
        try:
            from watchdog.observers import Observer
            from watchdog.events import FileSystemEventHandler
            import time
            
            class ThemeChangeHandler(FileSystemEventHandler):
                def __init__(self, builder):
                    self.builder = builder
                
                def on_modified(self, event):
                    if event.is_directory:
                        return
                    
                    if event.src_path.endswith(".json"):
                        self._handle_theme_change(event.src_path)
                    
                    elif event.src_path.endswith(".scss") or event.src_path.endswith(".css"):
                        self._handle_css_change(event.src_path)
                
                def _handle_theme_change(self, file_path):
                    print(f"\nDetected changes in {file_path}")
                    self.builder.build_all()
                
                def _handle_css_change(self, file_path):
                    print(f"Detected changes in CSS: {file_path}")
                    # Implement CSS rebuild logic
                    
            observer = Observer()
            event_handler = ThemeChangeHandler(self)
            
            input_path = self.config.input_dir
            observer.schedule(event_handler, input_path, recursive=True)
            observer.start()
            
            try:
                while True:
                    time.sleep(1)
            except KeyboardInterrupt:
                print("\nStopping watch mode.")
            
            observer.stop()
            observer.join()
        
        except ImportError:
            print("Watchdog library not installed. Install with: pip install watchdog")
            print("Falling back to manual rebuilds.")
    
    def serve(self, port: int = 8000):
        """Serve the build output as a local server."""
        print(f"Starting local server on port {port}...")
        
        try:
            from http.server import SimpleHTTPRequestHandler, HTTPServer
            import socketserver
            
            class CORSRequestHandler(SimpleHTTPRequestHandler):
                def end_headers(self):
                    self.send_header("Access-Control-Allow-Origin", "*")
                    self.send_header("Access-Control-Allow-Methods", "GET")
                    super().end_headers()
            
            address = ("", port)
            httpd = socketserver.TCPServer(address, CORSRequestHandler)
            print(f"Serving at http://localhost:{port}")
            print("Press Ctrl+C to stop.")
            
            try:
                httpd.serve_forever()
            except KeyboardInterrupt:
                print("\nStopping server.")
            
            httpd.server_close()
        
        except ImportError:
            print("Could not start server. Install http.server or use a web server.")
    
    @classmethod
    def from_command_line(cls) -> "Builder":
        """Create a Builder instance from command line arguments."""
        import argparse
        
        parser = argparse.ArgumentParser(description="Theme Builder CLI")
        parser.add_argument(
            "--input',
            help="Input directory (default: src)",
            default="src"
        )
        parser.add_argument(
            "--output',
            help="Output directory (default: dist)",
            default="dist"
        )
        parser.add_argument(
            "--target',
            choices=["dev", "prod"],
            default="prod",
            help="Build target: dev or prod"
        )
        parser.add_argument(
            "--minify',
            action="store_true",
            help="Minify output files"
        )
        parser.add_argument(
            "--sourcemaps',
            action="store_true",
            help="Generate sourcemaps"
        )
        parser.add_argument(
            "files',
            nargs="*",
            help="Specific theme files to build (optional)"
        )
        
        args = parser.parse_args()
        
        builder = cls(BuildConfig(
            input_dir=args.input,
            output_dir=args.output,
            target=BuildTarget(args.target),
            minify=args.minify,
            sourcemaps=args.sourcemaps
        ))
        
        return builder


if __name__ == "__main__":
    builder = Builder.from_command_line()
    builder.build_all()
```

### 21. Continuous Integration

```yaml
# .github/workflows/build-and-test.yml
name: Build and Test Theme Project

on:
  push:
    branches: [ "main", "develop" ]
  pull_request:
    branches: [ "main", "develop" ]
  schedule:
    - cron: "0 0 * * 0"  #每周日凌晨

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8, 3.9, 3.10]
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install .
      
      - name: Lint code
        run: |
          flake8 theme_builder tests
          black --check theme_builder tests
      
      - name: Type checking
        run: |
          mypy theme_builder
      
      - name: Run tests
        run: |
          python -m pytest tests --cov=theme_builder --cov-report=xml
      
      - name: Build project
        run: |
          python setup.py build
          python setup.py sdist bdist_wheel
      
      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: dist/
          
  build-docker:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Build Docker image
        run: |
          docker build -t theme-builder:latest .
      
      - name: Run Docker tests
        run: |
          docker run --rm theme-builder:latest --help > /dev/null
          echo "Docker tests passed"
          
  deploy:
    runs-on: ubuntu-latest
    needs: [build, build-docker]
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install setuptools wheel twine
          
      - name: Build distribution
        run: |
          python setup.py sdist bdist_wheel
          
      - name: Upload to PyPI
        run: |
          twine upload dist/*
        env:
          TWINE_USERNAME: ${{ secrets.PYPI_USERNAME }}
          TWINE_PASSWORD: ${{ secrets.PYPI_PASSWORD }}
          
      - name: Deploy to GitHub Pages
        run: |
          cd docs
          git config --global user.email "action@github.com"
          git config --global user.name "GitHub Action"
          git add .
          git commit -m "Deploying docs"
          git push --force --set-upstream origin gh-pages
```

### 22. Documentation

```
Theme Builder Documentation
===========================

Complete guide to using the Theme Builder CLI and API.

Table of Contents
-----------------
1. Getting Started
2. Configuration Guide
3. Command Reference
4. API Documentation
5. Advanced Usage
6. Examples
7. Troubleshooting

1. Getting Started
------------------

1.1 Installation

You can install Theme Builder using pip:

```bash
pip install theme-builder
```

Or from source:

```bash
git clone https://github/theme-builder.git
cd theme-builder
pip install .
```

1.2 Project Structure

```
your-project/
├── themes/              # Theme configuration files
│   ├── default.json     # Main theme configuration
│   └── dark-mode.json   # Alternative theme
├── src/                 # Source code (if applicable)
├── dist/                # Built output
│   ├── themes/          # Compiled theme files
│   └── assets/          # Generated CSS, JS, etc.
├── docs/                # Documentation
└── tests/               # Testing suite
```

1.3 Basic Usage

```bash
# Create a new theme
theme-builder new --name "My Awesome Theme" --type modern

# Build all themes
theme-builder build

# Preview a theme
theme-builder preview

# Validate theme configuration
theme-builder validate
```

2. Configuration Guide
---------------------

2.1 Theme Configuration File

A typical theme configuration file (`themes/default.json`):

```json
{
  "name": "Modern Dashboard",
  "description": "A clean, professional dashboard theme",
  "author": "Your Name",
  "version": "1.0.0",
  
  "colors": {
    "primary": "#6366f1",
    "primary-dark": "#4f46e5",
    "secondary": "#8b5cf6",
    "accent": "#ec4899",
    "warning": "#f59e0b",
    "error": "#f87171",
    "success": "#10b981",
    "background": "#f9fafb",
    "text": "#1e293b",
    "card": "#ffffff"
  },
  
  "typography": {
    "font_family": "'Inter', 'Helvetica', sans-serif",
    "font_size": "16px",
    "line_height": "1.5",
    "letter_spacing": "0.5px"
  },
  
  "components": {
    "button": {
      "base": {
        "padding": "12px 24px",
        "border_radius": "8px",
        "transition": "all 0.3s ease"
      },
      "variants": {
        "primary": {
          "modifier": "primary",
          "styles": {
            "background_color": "var(--primary)",
            "color": "var(--text)",
            "&:hover": {
              "background_color": "var(--primary-dark)"
            }
          }
        },
        "secondary": {
          "modifier": "secondary",
          "styles": {
            "background_color": "var(--secondary)",
            "color": "var(--text)",
            "&:hover": {
              "background_color": "var(--secondary-dark)"
            }
          }
        }
      }
    }
  },
  
  "accessibility": {
    "contrast": {
      "text_on_background": 4.8,
      "primary_button": 5.2
    },
    "keyboard": {
      "focus_visible": "2px solid var(--primary)",
      "focus_ring": "3px solid var(--primary-dark)"
    }
  },
  
  "responsive": {
    "breakpoints": {
      "mobile": "768px",
      "tablet": "1024px",
      "laptop": "1440px",
      "desktop": "1920px"
    },
    "queries": {
      "mobile": {
        ".sidebar": "display: none !important;",
        ".header": "padding: 24px !important;"
      },
      "tablet": {
        ".container": "grid-template-columns: 1fr 2fr !important;"
      }
    }
  }
}
```

2.2 Configuration Key Reference

| Key | Description |
|-----|-------------|
| `name` | Theme name |
| `description` | Detailed theme description |
| `author` | Author/creator |
| `version` | Theme version |
| `colors` | Color palette |
| `typography` | Typography settings |
| `components` | Component styles |
| `accessibility` | Accessibility settings |
| `responsive` | Responsive design settings |
| `animations` | Animation definitions |

2.3 Color Configuration

Colors define the visual identity of your theme. You can specify:

```json
"colors": {
  "primary": "#6366f1",          // Main primary color
  "primary-dark": "#4f46e5",     // Dark variant for hover/states
  "secondary": "#8b5cf6",        // Secondary color
  "accent": "#ec4899",           // Accent color for highlights
  "warning": "#f59e0b",          // Warning color
  "error": "#f87171",            // Error color
  "success": "#10b981",          // Success color
  "background": "#f9fafb",       // Background color
  "text": "#1e293b",             // Primary text color
  "card": "#ffffff",             // Card background color
  "border": "#e5e7eb",           // Border color
  "separator": "#e5e7eb"         // Separator color
}
```

2.4 Typography Configuration

Typography defines the text styles throughout your application.

```json
"typography": {
  "font_family": "'Inter', 'Helvetica', sans-serif",
  "font_size": "16px",
  "line_height": "1.5",
  "letter_spacing": "0.5px",
  
  "variants": {
    "heading": {
      "font_size": "24px",
      "line_height": "1.25",
      "font_weight": "700"
    },
    "subheading": {
      "font_size": "20px",
      "line_height": "1.35",
      "font_weight": "600"
    },
    "body": {
      "font_size": "16px",
      "line_height": "1.5",
      "font_weight": "400"
    },
    "caption": {
      "font_size": "12px",
      "line_height": "1.35",
      "font_weight": "400"
    }
  }
}
```

2.5 Components Configuration

Components define the UI elements and their styles.

```json
"components": {
  "button": {
    "base": {
      "padding": "12px 24px",
      "border_radius": "8px",
      "transition": "all 0.3s ease"
    },
    "variants": {
      "primary": {
        "modifier": "primary",
        "styles": {
          "background_color": "var(--primary)",
          "color": "var(--text)",
          "&:hover": {
            "background_color": "var(--primary-dark)"
          }
        }
      },
      "secondary": {
        "modifier": "secondary",
        "styles": {
          "background_color": "var(--secondary)",
          "color": "var(--text)",
          "&:hover": {
            "background_color": "var(--secondary-dark)"
          }
        }
      },
      "text": {
        "modifier": "text",
        "styles": {
          "background_color": "transparent",
          "border": "2px solid var(--primary)",
          "color": "var(--primary)",
          "&:hover": {
            "background_color": "var(--primary-light)"
          }
        }
      }
    }
  },
  "card": {
    "base": {
      "background_color": "var(--card)",
      "border_radius": "12px",
      "box_shadow": "0 4px 6px -2px rgba(0, 0, 0, 0.1)"
    },
    "variants": {
      "elevated": {
        "modifier": "elevated",
        "styles": {
          "box_shadow": "0 8px 20px rgba(0, 0, 0, 0.2)"
        }
      }
    }
  }
}
```

2.6 Accessibility Configuration

Accessibility ensures your theme is usable for everyone.

```json
"accessibility": {
  "contrast": {
    "text_on_background": 4.8,          // Minimum contrast ratio (WCAG AA: 4.5)
    "primary_button": 5.2,              // Button text on background
    "link": 4.8,                        // Links
    "icon_on_background": 3.0           // Icons on background (lower acceptable)
  },
  "keyboard": {
    "focus_visible": "2px solid var(--primary)",  // Focus indicator
    "focus_ring": "3px solid var(--primary-dark)", // Larger focus ring
    "focus_outline": "none"                        // Remove default outline
  },
  "aria": {
    "required_label": "Required",
    "loading_state": "Loading...",
    "error_state": "Error",
    "success_state": "Success"
  }
}
```

2.7 Responsive Design Configuration

Responsive settings control how your theme adapts to different screen sizes.

```json
"responsive": {
  "breakpoints": {
    "mobile": "768px",
    "tablet": "1024px",
    "laptop": "1440px",
    "desktop": "1920px"
  },
  "queries": {
    "mobile": {
      ".sidebar": "display: none !important;",
      ".header": "padding: 24px !important;"
    },
    "tablet": {
      ".container": "grid-template-columns: 1fr !important;",
      ".card": "max-width: 100% !important;"
    },
    "laptop": {
      ".container": "grid-template-columns: repeat(2, 1fr) !important;"
    },
    "desktop": {
      ".container": "grid-template-columns: repeat(3, 1fr) !important;"
    }
  }
}
```

3. Command Reference
--------------------

3.1 `theme-builder new`

Create a new theme configuration.

```bash
Usage: theme-builder new [OPTIONS]

  Creates a new theme configuration file.

Options:
  --name TEXT           Theme name
  --type [modern|classic|minimal|dark|flat]
                        Theme type (default: modern)
  --output PATH         Output directory (default: ./themes)
  --overwrite           Overwrite existing file if it exists
  --template TEXT       Template to use (default: default)
```

Example:

```bash
theme-builder new --name "My Dark Theme" --type dark --output ./themes
```

3.2 `theme-builder build`

Build themes for production.

```bash
Usage: theme-builder build [OPTIONS]

  Builds all themes into optimized output.

Options:
  --input PATH          Input directory (default: ./src)
  --output PATH         Output directory (default: ./dist)
  --minify              Minify output files
  --sourcemaps          Generate sourcemaps
  --target [dev|prod]   Build target (default: prod)
  --verbose             Verbose output
```

Example:

```bash
theme-builder build --input ./themes --output ./dist --minify
```

3.3 `theme-builder preview`

Preview a theme in the browser.

```bash
Usage: theme-builder preview [OPTIONS] [THEME]

  Starts a local server to preview the theme.

Options:
  --port INTEGER        Port to use (default: 8000)
  --host TEXT           Host to bind (default: 0.0.0.0)
  --theme TEXT          Theme to preview
  --config PATH         Configuration file path
```

Example:

```bash
theme-builder preview --theme default --port 3000
```

3.4 `theme-builder validate`

Validate theme configuration files.

```bash
Usage: theme-builder validate [OPTIONS] [FILES...]

  Validates one or more theme configuration files.

Options:
  --strict              Enable strict validation
  --output PATH         Output validation results to file
  --quiet               Quiet mode (only show errors)
```

Example:

```bash
theme-builder validate themes/default.json themes/dark-mode.json
```

3.5 `theme-builder convert`

Convert theme between formats.

```bash
Usage: theme-builder convert [OPTIONS] [INPUT] [OUTPUT]

  Converts theme configuration between formats.

Options:
  --from FORMAT         Input format (json or yaml)
  --to FORMAT           Output format (json or yaml)
  --schema              Validate against schema
```

Example:

```bash
theme-builder convert --from yaml --to json themes/default.yaml themes/default.json
```

3.6 `theme-builder diff`

Compare two theme configurations.

```bash
Usage: theme-builder diff [OPTIONS] [FILE1] [FILE2]

  Compares two theme configuration files.

Options:
  --output PATH         Output diff to file
  --color               Color output
  --ignore-whitespace   Ignore whitespace differences
```

Example:

```bash
theme-builder diff themes/default.json themes/updated.json
```

4. API Documentation
-------------------

4.1 Core Module

```python
# theme_builder/__init__.py

from .builder import Builder, BuildConfig, BuildTarget
from .validator import ThemeValidator, ValidationResults
from .compiler import ThemeCompiler
from .translator import ThemeTranslator
from .optimizer import ThemeOptimizer
from .exceptions import ThemeBuilderError

__all__ = [
    "Builder",
    "BuildConfig",
    "BuildTarget",
    "ThemeValidator",
    "ValidationResults",
    "ThemeCompiler",
    "ThemeTranslator",
    "ThemeOptimizer",
    "ThemeBuilderError"
]
```

4.2 Builder Class

```python
# theme_builder/builder.py

from __future__ import annotations
import os
import json
import shutil
from dataclasses import dataclass, field
from enum import Enum
from typing import Dict, Any, Optional, List
from .validator import ThemeValidator


@dataclass
class BuildConfig:
    """Configuration for the build process."""
    input_dir: str = "src"
    output_dir: str = "dist"
    theme_name: str = "default-theme"
    target: BuildTarget = BuildTarget.PRODUCTION
    minify: bool = True
    sourcemaps: bool = False


class BuildTarget(Enum):
    """Build target environments."""
    DEVELOPMENT = "dev"
    PRODUCTION = "prod"


class Builder:
    """
    Comprehensive build system for theme projects.
    Handles compilation, optimization, and packaging.
    """
    
    def __init__(self, config: Optional[BuildConfig] = None):
        self.config = config or BuildConfig()
        self.build_id: Optional[str] = None
        self.timestamp: Optional[str] = None
    
    def prepare_build(self):
        """Prepare for a new build."""
        self.timestamp = self._get_timestamp()
        self.build_id = self._generate_build_id()
        
        print(f"Preparing build [{self.build_id}]...")
        print(f"Time: {self.timestamp}")
        
        # Create output directory
        os.makedirs(self.config.output_dir, exist_ok=True)
        
        # Create build-specific directories
        os.makedirs(os.path.join(self.config.output_dir, "themes"), exist_ok=True)
        os.makedirs(os.path.join(self.config.output_dir, "assets"), exist_ok=True)
        os.makedirs(os.path.join(self.config.output_dir, "tmp"), exist_ok=True
    
    def _get_timestamp(self) -> str:
        """Get current timestamp for build identification."""
        from datetime import datetime
        return datetime.now().isoformat(" ", "seconds")
    
    def _generate_build_id(self) -> str:
        """Generate a unique build ID."""
        import uuid
        return str(uuid.uuid4())[:8]
    
    def compile_theme(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Compile a theme configuration.
        
        Args:
            theme_config: Raw theme configuration
            
        Returns:
            Compiled theme configuration
        """
        print(f"Compiling theme: {theme_config.get('name', 'untitled')}...")
        
        # Validate theme
        validator = ThemeValidator()
        validation_results = validator.validate(theme_config)
        
        if not validation_results.get("valid", True):
            print("Skipping compilation due to validation errors.")
            return {}
        
        # Optimize theme configuration
        optimized = {
            "metadata": {
                "name": theme_config.get("name"),
                "description": theme_config.get("description"),
                "version": theme_config.get("version", "1.0.0"),
                "build_id": self.build_id,
                "timestamp": self.timestamp
            },
            "colors": self._compile_colors(theme_config.get("colors", {})),
            "typography": self._compile_typography(theme_config.get("typography", {})),
            "components": self._compile_components(theme_config.get("components", {})),
            "accessibility": self._compile_accessibility(theme_config.get("accessibility", {})),
            "responsive": self._compile_responsive(theme_config.get("responsive", {})),
            "animations": self._compile_animations(theme_config.get("animations", {}))
        }
        
        # Add validation results
        optimized["validation"] = {
            "valid": validation_results.get("valid", True),
            "warnings": validation_results.get("warnings", []),
            "errors": validation_results.get("errors", [])
        }
        
        return optimized
    
    def _compile_colors(self, colors: Dict[str, Any]) -> Dict[str, Any]:
        """Compile colors section."""
        compiled = {}
        
        for key, value in colors.items():
            if isinstance(value, str) and value.startswith(("var(", "#", "rgb(", "hsl(")):
                compiled[key] = value
            else:
                # Convert to CSS variable format if it's a simple value
                compiled[key] = f"var(--{key.lower()})", value
        
        return compiled
    
    def _compile_typography(self, typography: Dict[str, Any]) -> Dict[str, Any]:
        """Compile typography section."""
        compiled = {
            "font": {
                "family": typography.get("font_family", "sans-serif"),
                "size": typography.get("font_size", "16px"),
                "line_height": typography.get("line_height", "1.5"),
                "letter_spacing": typography.get("letter_spacing", "normal")
            }
        }
        
        # Include variant-specific styles
        if "variants" in typography:
            compiled["variants"] = typography["variants"]
        
        return compiled
    
    def _compile_components(self, components: Dict[str, Any]) -> Dict[str, Any]:
        """Compile components section."""
        compiled = {}
        
        for component, config in components.items():
            compiled[component] = {
                "base": self._compile_base_styles(config.get("base", {})),
                "variants": self._compile_variants(config.get("variants", {}))
            }
        
        return compiled
    
    def _compile_base_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Compile base styles for a component."""
        return {k: v for k, v in styles.items() if v is not None}
    
    def _compile_variants(self, variants: Dict[str, Any]) -> Dict[str, Any]:
        """Compile variant styles for a component."""
        compiled = {}
        
        for variant, config in variants.items():
            compiled[variant] = {
                "modifier": config.get("modifier"),
                "styles": self._compile_styles(config.get("styles", {}))
            }
        
        return compiled
    
    def _compile_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Compile individual styles."""
        return {k: v for k, v in styles.items() if v is not None}
    
    def _compile_accessibility(self, accessibility: Dict[str, Any]) -> Dict[str, Any]:
        """Compile accessibility settings."""
        return {
            "contrast": accessibility.get("contrast", {}),
            "keyboard": accessibility.get("keyboard", {}),
            "aria": accessibility.get("aria", {})
        }
    
    def _compile_responsive(self, responsive: Dict[str, Any]) -> Dict[str, Any]:
        """Compile responsive design settings."""
        return {
            "breakpoints": self._compile_breakpoints(responsive.get("breakpoints", {})),
            "queries": responsive.get("queries", {})
        }
    
    def _compile_breakpoints(self, breakpoints: Dict[str, Any]) -> Dict[str, Any]:
        """Compile breakpoint definitions."""
        compiled = {}
        
        for name, value in breakpoints.items():
            if isinstance(value, str):
                # Parse breakpoint string (e.g., "768px", "min-width: 1024px")
                if value.startswith(("min-width:", "max-width:")):
                    compiled[name] = value
                else:
                    compiled[name] = f"min-width: {value}"
            # Add more breakpoint parsing logic as needed
        
        return compiled
    
    def _compile_animations(self, animations: Dict[str, Any]) -> Dict[str, Any]:
        """Compile animations section."""
        compiled = {}
        
        for name, keyframes in animations.items():
            compiled[name] = {
                "keyframes": self._format_keyframes(keyframes),
                "duration": keyframes.get("duration", "0.5s"),
                "timing_function": keyframes.get("timing_function", "ease"),
                "iteration_count": keyframes.get("iteration_count", "1"),
                "direction": keyframes.get("direction", "normal")
            }
        
        return compiled
    
    def _format_keyframes(self, keyframes: Dict[str, Any]) -> Dict[str, Any]:
        """Format animation keyframes."""
        formatted = {}
        
        for selector, styles in keyframes.items():
            formatted[selector] = {
                "properties": {k: v for k, v in styles.items() if k != "selector"},
                "easing": styles.get("easing")
            }
        
        return formatted
    
    def build_all(self):
        """Build all themes in the project."""
        self.prepare_build()
        
        # Collect all theme configuration files
        theme_files = self._find_theme_files()
        
        if not theme_files:
            print("No theme files found to build.")
            return
        
        build_results = {
            "summary": {
                "themes_processed": 0,
                "themes_skipped": 0,
                "errors": 0
            },
            "details": []
        }
        
        for file_path in theme_files:
            try:
                self._build_single_theme(file_path, build_results)
            except Exception as e:
                print(f"Error building {file_path}: {str(e)}")
                build_results["summary"]["errors"] += 1
        
        self._generate_build_summary(build_results)
    
    def _find_theme_files(self) -> List[str]:
        """Find all theme configuration files in the input directory."""
        theme_files = []
        
        input_path = self.config.input_dir
        if not os.path.exists(input_path):
            print(f"Input directory not found: {input_path}")
            return theme_files
        
        for root, dirs, files in os.walk(input_path):
            for file in files:
                if file.endswith(".json"):
                    theme_files.append(os.path.join(root, file))
        
        return theme_files
    
    def _build_single_theme(self, file_path: str, results: Dict[str, Any]):
        """
        Build a single theme file.
        
        Args:
            file_path: Path to theme file
            results: Build results dictionary
        """
        print(f"\nProcessing theme: {file_path}")
        
        # Load theme configuration
        with open(file_path, "r") as file:
            theme_config = json.load(file)
        
        # Compile theme
        compiled_theme = self.compile_theme(theme_config)
        
        if not compiled_theme:
            print(f"Skipping {file_path} - no valid configuration found.")
            results["summary"]["themes_skipped"] += 1
            return
        
        # Generate output file path
        relative_path = os.path.relpath(file_path, self.config.input_dir)
        base_name = os.path.splitext(os.path.basename(file_path))[0]
        output_path = os.path.join(
            self.config.output_dir,
            "themes",
            f"{base_name}-{self.build_id}.json"
        )
        
        # Save compiled theme
        with open(output_path, "w") as file:
            json.dump(compiled_theme, file, indent=2)
        
        # Build associated CSS
        self.build_theme_css(theme_config, base_name)
        
        results["summary"]["themes_processed"] += 1
        results["details"].append({
            "file": file_path,
            "output": output_path,
            "status": "success",
            "compiled_size": len(json.dumps(compiled_theme))
        })
        
        print(f"✓ Built {file_path}")
        print(f"  → Output: {output_path}")
        print(f"  → Size: {len(json.dumps(compiled_theme))} bytes")
    
    def build_theme_css(self, theme_config: Dict[str, Any], theme_name: str):
        """
        Build CSS file for a theme.
        
        Args:
            theme_config: Theme configuration
            theme_name: Theme name for file naming
        """
        css_content = self._generate_css_from_config(theme_config)
        
        if self.config.minify:
            css_content = self._minify_css(css_content)
        
        css_output_path = os.path.join(
            self.config.output_dir,
            "assets",
            f"{theme_name}-{self.build_id}.css"
        )
        
        with open(css_output_path, "w") as css_file:
            css_file.write(css_content)
        
        print(f"  → CSS built: {css_output_path}")
    
    def _generate_css_from_config(self, theme_config: Dict[str, Any]) -> str:
        """Generate CSS from theme configuration."""
        css = "/* Generated CSS - do not edit manually */\n\n"
        
        # CSS Variables
        css += ":root {\n"
        for key, value in theme_config.get("colors", {}).items():
            css += f"  --{key.lower()}: {value};\n"
        css += "}\n\n"
        
        # Base styles
        css += "body {\n"
        css += f"  font-family: {theme_config.get('typography', {}).get('font_family', 'sans-serif')};\n"
        css += f"  font-size: {theme_config.get('typography', {}).get('font_size', '16px')};\n"
        css += f"  background-color: var(--background);\n"
        css += "}\n\n"
        
        # Button styles
        if "components" in theme_config and "button" in theme_config["components"]:
            button_config = theme_config["components"]["button"]
            css += "button {\n"
            css += f"  background-color: var(--primary);\n"
            css += f"  color: var(--text);\n"
            css += f"  padding: {button_config.get('padding', '12px 24px')};\n"
            css += f"  border-radius: {button_config.get('border_radius', '8px')};\n"
            css += "}\n\n"
        
        return css
    
    def _minify_css(self, css: str) -> str:
        """Minify CSS content."""
        # Remove comments
        css = "\n".join([line for line in css.split("\n") if not line.strip().startswith(("/*", "*"))])
        
        # Remove whitespace
        css = " ".join(css.split())
        
        return css
    
    def _generate_build_summary(self, results: Dict[str, Any]):
        """Generate a build summary report."""
        summary_path = os.path.join(
            self.config.output_dir,
            "build-summary.json"
        )
        
        with open(summary_path, "w") as summary_file:
            json.dump(results, summary_file, indent=2)
        
        print(f"\nBuild Summary:")
        print(f"══════════════════════════════════════════════════════")
        print(f"Build ID: {self.build_id}")
        print(f"Timestamp: {self.timestamp}")
        print("")
        print(f"Themes Processed: {results['summary']['themes_processed']}")
        print(f"Themes Skipped: {results['summary']['themes_skipped']}")
        print(f"Errors: {results['summary']['errors']}")
        print("")
        print(f"Total Output Size: {self._calculate_total_size()} bytes")
        print(f"Output Directory: {self.config.output_dir}")
        print("══════════════════════════════════════════════════════")
    
    def _calculate_total_size(self) -> int:
        """Calculate the total size of all build outputs."""
        total_size = 0
        output_path = self.config.output_dir
        
        for root, dirs, files in os.walk(output_path):
            for file in files:
                file_path = os.path.join(root, file)
                total_size += os.path.getsize(file_path)
        
        return total_size
    
    def clean(self):
        """Clean the build directory."""
        print(f"Cleaning build directory: {self.config.output_dir}")
        if os.path.exists(self.config.output_dir):
            shutil.rmtree(self.config.output_dir)
        os.makedirs(self.config.output_dir, exist_ok=True)
    
    def watch(self):
        """Watch for changes and rebuild automatically."""
        print("Starting watch mode...")
        print("Press Ctrl+C to exit.")
        
        try:
            from watchdog.observers import Observer
            from watchdog.events import FileSystemEventHandler
            import time
            
            class ThemeChangeHandler(FileSystemEventHandler):
                def __init__(self, builder):
                    self.builder = builder
                
                def on_modified(self, event):
                    if event.is_directory:
                        return
                    
                    if event.src_path.endswith(".json"):
                        self._handle_theme_change(event.src_path)
                    
                    elif event.src_path.endswith(".scss") or event.src_path.endswith(".css"):
                        self._handle_css_change(event.src_path)
                
                def _handle_theme_change(self, file_path):
                    print(f"\nDetected changes in {file_path}")
                    self.builder.build_all()
                
                def _handle_css_change(self, file_path):
                    print(f"Detected changes in CSS: {file_path}")
                    # Implement CSS rebuild logic
                    
            observer = Observer()
            event_handler = ThemeChangeHandler(self)
            
            input_path = self.config.input_dir
            observer.schedule(event_handler, input_path, recursive=True)
            observer.start()
            
            try:
                while True:
                    time.sleep(1)
            except KeyboardInterrupt:
                print("\nStopping watch mode.")
            
            observer.stop()
            observer.join()
        
        except ImportError:
            print("Watchdog library not installed. Install with: pip install watchdog")
            print("Falling back to manual rebuilds.")
    
    def serve(self, port: int = 8000):
        """Serve the build output as a local server."""
        print(f"Starting local server on port {port}...")
        
        try:
            from http.server import SimpleHTTPRequestHandler, HTTPServer
            import socketserver
            
            class CORSRequestHandler(SimpleHTTPRequestHandler):
                def end_headers(self):
                    self.send_header("Access-Control-Allow-Origin", "*")
                    self.send_header("Access-Control-Allow-Methods", "GET")
                    super().end_headers()
            
            address = ("", port)
            httpd = socketserver.TCPServer(address, CORSRequestHandler)
            print(f"Serving at http://localhost:{port}")
            print("Press Ctrl+C to stop.")
            
            try:
                httpd.serve_forever()
            except KeyboardInterrupt:
                print("\nStopping server.")
            
            httpd.server_close()
        
        except ImportError:
            print("Could not start server. Install http.server or use a web server.")
```

4.3 Validator Class

```python
# theme_builder/validator.py

from __future__ import annotations
import jsonschema
from dataclasses import dataclass
from typing import Dict, Any, List, Optional
from .exceptions import ThemeBuilderError


@dataclass
class ValidationResults:
    """Results from theme validation."""
    valid: bool = True
    errors: List[str] = field(default_factory=list)
    warnings: List[str] = field(default_factory=list)


class ThemeValidator:
    """
    Validates theme configuration files against a predefined schema.
    Ensures themes meet quality and consistency standards.
    """
    
    # JSON Schema for theme validation
    SCHEMA = {
        "$schema": "http://json-schema.org/draft-07/schema#",
        "title": "Theme Configuration Schema",
        "description": "Schema for validating theme configuration files",
        "type": "object",
        "properties": {
            "name": {
                "type": "string",
                "description": "Name of the theme",
                "minLength": 1
            },
            "description": {
                "type": "string",
                "description": "Detailed description of the theme"
            },
            "author": {
                "type": "string",
                "description": "Author/creator of the theme"
            },
            "version": {
                "type": "string",
                "description": "Theme version (semantic versioning recommended)"
            },
            "colors": {
                "type": "object",
                "description": "Color palette configuration",
                "properties": {
                    "primary": {"type": "string", "description": "Primary color"},
                    "primary-dark": {"type": "string", "description": "Dark variant of primary color"},
                    "secondary": {"type": "string", "description": "Secondary color"},
                    "accent": {"type": "string", "description": "Accent color"},
                    "warning": {"type": "string", "description": "Warning color"},
                    "error": {"type": "string", "description": "Error color"},
                    "success": {"type": "string", "description": "Success color"},
                    "background": {"type": "string", "description": "Background color"},
                    "text": {"type": "string", "description": "Primary text color"},
                    "card": {"type": "string", "description": "Card background color"},
                    "border": {"type": "string", "description": "Border color"},
                    "separator": {"type": "string", "description": "Separator color"}
                },
                "additionalProperties": False
            },
            "typography": {
                "type": "object",
                "description": "Typography configuration",
                "properties": {
                    "font_family": {"type": "string", "description": "Primary font family"},
                    "font_size": {"type": "string", "description": "Base font size"},
                    "line_height": {"type": "string", "description": "Base line height"},
                    "letter_spacing": {"type": "string", "description": "Base letter spacing"},
                    "variants": {
                        "type": "object",
                        "description": "Typography variants",
                        "additionalProperties": {
                            "type": "object",
                            "description": "Variant properties"
                        }
                    }
                },
                "required": ["font_family", "font_size"]
            },
            "components": {
                "type": "object",
                "description": "Component styles",
                "properties": {
                    "button": {
                        "type": "object",
                        "description": "Button component configuration",
                        "properties": {
                            "base": {
                                "type": "object",
                                "description": "Base button styles"
                            },
                            "variants": {
                                "type": "object",
                                "description": "Button variants"
                            }
                        }
                    },
                    "card": {
                        "type": "object",
                        "description": "Card component configuration"
                    }
                },
                "additionalProperties": False
            },
            "accessibility": {
                "type": "object",
                "description": "Accessibility settings",
                "properties": {
                    "contrast": {
                        "type": "object",
                        "description": "Contrast ratios",
                        "properties": {
                            "text_on_background": {
                                "type": "number",
                                "minimum": 4.5,
                                "description": "Minimum contrast ratio for text on background (WCAG AA: 4.5)"
                            },
                            "primary_button": {
                                "type": "number",
                                "minimum": 4.5,
                                "description": "Contrast ratio for button text"
                            }
                        },
                        "additionalProperties": False
                    },
                    "keyboard": {
                        "type": "object",
                        "description": "Keyboard accessibility settings"
                    },
                    "aria": {
                        "type": "object",
                        "description": "ARIA settings"
                    }
                },
                "additionalProperties": False
            },
            "responsive": {
                "type": "object",
                "description": "Responsive design settings",
                "properties": {
                    "breakpoints": {
                        "type": "object",
                        "description": "Breakpoint definitions"
                    },
                    "queries": {
                        "type": "object",
                        "description": "Responsive queries"
                    }
                }
            }
        },
        "required": ["name", "colors", "typography", "accessibility"]
    }
    
    def validate(self, theme_config: Dict[str, Any]) -> ValidationResults:
        """
        Validate a theme configuration against the schema.
        
        Args:
            theme_config: Theme configuration to validate
            
        Returns:
            ValidationResults object with validation outcome
        """
        results = ValidationResults()
        
        try:
            jsonschema.validate(instance=theme_config, schema=self.SCHEMA)
            results.valid = True
            results.errors = []
            results.warnings = []
            
        except jsonschema.exceptions.ValidationError as e:
            results.valid = False
            results.errors = self._format_validation_errors(e)
            results.warnings = []
            
        return results
    
    def _format_validation_errors(self, error: jsonschema.exceptions.ValidationError) -> List[str]:
        """Format validation errors into human-readable list."""
        errors = []
        current_error = error
        
        while current_error:
            message = f"- {current_error.message}"
            if current_error.validator:
                message += f" [Validator: {current_error.validator}]"
            if current_error.validator_value:
                message += f" [Expected: {current_error.validator_value}]"
            errors.append(message)
            current_error = current_error.context
        
        return errors
    
    def validate_all(self, themes_dir: str) -> Dict[str, Any]:
        """
        Validate all theme configuration files in a directory.
        
        Args:
            themes_dir: Directory containing theme files
            
        Returns:
            Summary of validation results
        """
        results = {
            "summary": {
                "total_themes": 0,
                "valid_themes": 0,
                "invalid_themes": 0,
                "errors_count": 0,
                "warnings_count": 0
            },
            "details": []
        }
        
        theme_files = self._find_theme_files(themes_dir)
        results["summary"]["total_themes"] = len(theme_files)
        
        for file_path in theme_files:
            try:
                with open(file_path, "r") as file:
                    theme_config = json.load(file)
                
                validator = ThemeValidator()
                validation = validator.validate(theme_config)
                
                file_results = {
                    "file": file_path,
                    "valid": validation.valid,
                    "errors": validation.errors,
                    "warnings": validation.warnings
                }
                
                results["details"].append(file_results)
                
                if validation.valid:
                    results["summary"]["valid_themes"] += 1
                else:
                    results["summary"]["invalid_themes"] += 1
                    results["summary"]["errors_count"] += len(validation.errors)
                    results["summary"]["warnings_count"] += len(validation.warnings)
                
            except Exception as e:
                print(f"Error validating {file_path}: {str(e)}")
                results["summary"]["invalid_themes"] += 1
                results["summary"]["errors_count"] += 1
        
        return results
    
    def _find_theme_files(self, directory: str) -> List[str]:
        """Find all JSON theme files in a directory."""
        theme_files = []
        
        if not os.path.exists(directory):
            return theme_files
        
        for root, dirs, files in os.walk(directory):
            for file in files:
                if file.endswith(".json"):
                    theme_files.append(os.path.join(root, file))
        
        return theme_files


class StrictThemeValidator(ThemeValidator):
    """
    Strict validator with additional checks beyond the basic schema.
    Enforces best practices and additional constraints.
    """
    
    def validate(self, theme_config: Dict[str, Any]) -> ValidationResults:
        """
        Perform strict validation with additional checks.
        
        Args:
            theme_config: Theme configuration to validate
            
        Returns:
            ValidationResults object
        """
        results = ValidationResults()
        basic_validator = ThemeValidator()
        basic_results = basic_validator.validate(theme_config)
        
        results.valid = basic_results.valid
        results.errors = basic_results.errors.copy()
        results.warnings = basic_results.warnings.copy()
        
        if not basic_results.valid:
            return results
        
        # Additional strict checks
        self._check_color_completeness(theme_config, results)
        self._check_required_properties(theme_config, results)
        self._check_value_formats(theme_config, results)
        
        return results
    
    def _check_color_completeness(self, theme_config: Dict[str, Any], results: ValidationResults):
        """Check that all color properties are present."""
        required_colors = ["primary", "background", "text"]
        
        missing_colors = [color for color in required_colors 
                          if color not in theme_config.get("colors", {})]
        
        if missing_colors:
            results.errors.extend([
                f"Missing required color: {color}" for color in missing_colors
            ])
    
    def _check_required_properties(self, theme_config: Dict[str, Any], results: ValidationResults):
        """Check for properties that should always be present."""
        required_properties = {
            "typography": ["font_family", "font_size"],
            "colors": ["primary", "background", "text"]
        }
        
        for section, properties in required_properties.items():
            if section in theme_config:
                for prop in properties:
                    if prop not in theme_config[section]:
                        results.errors.append(
                            f"Missing required property in {section}: {prop}"
                        )
    
    def _check_value_formats(self, theme_config: Dict[str, Any], results: ValidationResults):
        """Check that values are in correct format."""
        # Check font sizes are valid
        if "typography" in theme_config:
            font_size = theme_config["typography"].get("font_size")
            if font_size and not self._is_valid_font_size(font_size):
                results.errors.append(
                    f"Invalid font size format: {font_size}"
                )
        
        # Check line heights are valid
        if "typography" in theme_config:
            line_height = theme_config["typography"].get("line_height")
            if line_height and not self._is_valid_line_height(line_height):
                results.errors.append(
                    f"Invalid line height format: {line_height}"
                )
    
    def _is_valid_font_size(self, value: str) -> bool:
        """Check if font size is valid."""
        valid_units = ["px", "em", "rem", "%"]
        if value.endswith(tuple(valid_units)):
            return True
        return False
    
    def _is_valid_line_height(self, value: str) -> bool:
        """Check if line height is valid."""
        try:
            float(value)
            return True
        except ValueError:
            return self._is_valid_font_size(value)


class AccessibilityValidator:
    """
    Validates accessibility-related aspects of a theme.
    Ensures contrast ratios and other accessibility requirements are met.
    """
    
    def validate(self, theme_config: Dict[str, Any]) -> ValidationResults:
        """
        Validate accessibility settings in a theme.
        
        Args:
            theme_config: Theme configuration to validate
            
        Returns:
            ValidationResults object
        """
        results = ValidationResults()
        accessibility = theme_config.get("accessibility", {})
        
        # Check contrast ratios
        self._validate_contrast_ratios(accessibility, results)
        
        # Check keyboard settings
        self._validate_keyboard_settings(accessibility, results)
        
        return results
    
    def _validate_contrast_ratios(self, accessibility: Dict[str, Any], results: ValidationResults):
        """Validate contrast ratios meet accessibility standards."""
        contrast_settings = accessibility.get("contrast", {})
        
        # WCAG AA minimum contrast ratio: 4.5:1
        # WCAG AAA: 6.5:1
        
        required_ratios = {
            "text_on_background": 4.5,
            "primary_button": 4.5,
            "link": 4.5,
            "icon_on_background": 3.0  # Lower threshold for icons
        }
        
        for ratio_name, min_ratio in required_ratios.items():
            ratio = contrast_settings.get(ratio_name)
            if ratio is not None:
                if ratio < min_ratio:
                    results.errors.append(
                        f"Contrast ratio {ratio_name} ({ratio}:1) is below "
                        f"minimum required {min_ratio}:1 (WCAG AA)"
                    )
            else:
                results.errors.append(
                    f"Missing contrast ratio: {ratio_name}"
                )
    
    def _validate_keyboard_settings(self, accessibility: Dict[str, Any], results: ValidationResults):
        """Validate keyboard accessibility settings."""
        keyboard_settings = accessibility.get("keyboard", {})
        
        # Ensure focus styles are defined
        if "focus_visible" not in keyboard_settings:
            results.warnings.append(
                "Focus visible style not defined. Consider adding a focus indicator "
                "for keyboard navigation."
            )
        
        # Check for missing focus outline removal
        if "focus_outline" not in keyboard_settings:
            results.warnings.append(
                "Focus outline not explicitly defined. Default outline may not be "
                "visually appealing."
            )


class ThemeBuilderError(Exception):
    """Base exception class for Theme Builder errors."""
    pass


class ConfigurationError(ThemeBuilderError):
    """Raised when there's a configuration error."""
    pass


class ValidationException(ThemeBuilderError):
    """Raised when validation fails."""
    pass


class BuildFailedError(ThemeBuilderError):
    """Raised when build process fails."""
    pass


class FileNotFoundError(ThemeBuilderError):
    """Raised when a file is not found."""
    pass


class ThemeNotFoundException(ThemeBuilderError):
    """Raised when a theme is not found."""
    pass


class CompilationError(ThemeBuilderError):
    """Raised when compilation fails."""
    pass


class MinificationError(ThemeBuilderError):
    """Raised when minification fails."""
    pass


class ServingError(ThemeBuilderError):
    """Raised when serving fails."""
    pass
```

4.4 Compiler Module

```python
# theme_builder/compiler.py

from __future__ import annotations
import os
import json
from typing import Dict, Any, List, Optional
from .builder import Builder, BuildConfig, BuildTarget
from .validator import ThemeValidator, ValidationResults
from .exceptions import ThemeBuilderError


class ThemeCompiler:
    """
    Handles the compilation of theme configurations.
    Transforms raw theme definitions into optimized output.
    """
    
    def __init__(self, builder: Optional[Builder] = None):
        self.builder = builder or Builder()
    
    def compile(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Compile a theme configuration.
        
        Args:
            theme_config: Raw theme configuration
            
        Returns:
            Compiled theme configuration
        """
        # Validate first
        validator = ThemeValidator()
        validation = validator.validate(theme_config)
        
        if not validation.valid:
            raise CompilationError("Cannot compile invalid theme", validation.errors)
        
        # Perform compilation
        return self._process_theme(theme_config)
    
    def _process_theme(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Process and transform the theme configuration.
        
        Args:
            theme_config: Original theme configuration
            
        Returns:
            Processed theme configuration
        """
        processed = {
            "metadata": self._compile_metadata(theme_config),
            "colors": self._compile_colors(theme_config),
            "typography": self._compile_typography(theme_config),
            "components": self._compile_components(theme_config),
            "accessibility": self._compile_accessibility(theme_config),
            "responsive": self._compile_responsive(theme_config),
            "animations": self._compile_animations(theme_config)
        }
        
        return processed
    
    def _compile_metadata(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile metadata section."""
        metadata = {
            "name": theme_config.get("name"),
            "description": theme_config.get("description"),
            "author": theme_config.get("author"),
            "version": theme_config.get("version", "1.0.0"),
            "build_id": self.builder.build_id,
            "timestamp": self.builder.timestamp
        }
        
        return {k: v for k, v in metadata.items() if v is not None}
    
    def _compile_colors(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile colors section with variable conversion."""
        colors = theme_config.get("colors", {})
        compiled = {}
        
        for key, value in colors.items():
            if isinstance(value, str) and value.startswith(("var(", "#", "rgb(", "hsl(")):
                compiled[key] = value
            else:
                compiled[key] = f"var(--{key.lower()})"
        
        return compiled
    
    def _compile_typography(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile typography section with unit standardization."""
        typography = theme_config.get("typography", {})
        compiled = {
            "font_family": typography.get("font_family"),
            "font_size": self._standardize_value(typography.get("font_size"), "16px"),
            "line_height": typography.get("line_height", "1.5"),
            "letter_spacing": typography.get("letter_spacing", "normal"),
            "variants": self._compile_variants(typography.get("variants", {}))
        }
        
        return {k: v for k, v in compiled.items() if v is not None}
    
    def _standardize_value(self, value: Optional[str], default: str) -> str:
        """Standardize a value with default fallback."""
        return value if value and value.strip() != "" else default
    
    def _compile_variants(self, variants: Dict[str, Any]) -> Dict[str, Any]:
        """Compile typography variants."""
        compiled = {}
        
        for variant_name, variant_config in variants.items():
            compiled_variant = {
                "font_size": self._standardize_value(variant_config.get("font_size"), 
                                                    variant_config.get("font_size", "16px")),
                "line_height": variant_config.get("line_height", "1.5"),
                "font_weight": variant_config.get("font_weight", "400"),
                "text_transform": variant_config.get("text_transform", "none"),
                "letter_spacing": variant_config.get("letter_spacing", "normal")
            }
            
            compiled[variant_name] = compiled_variant
        
        return compiled
    
    def _compile_components(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile components section."""
        components = theme_config.get("components", {})
        compiled = {}
        
        for component_name, component_config in components.items():
            compiled_component = {
                "base": self._compile_base_styles(component_config.get("base", {})),
                "variants": self._compile_component_variants(
                    component_config.get("variants", {})
                )
            }
            
            compiled[component_name] = compiled_component
        
        return compiled
    
    def _compile_base_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Compile base styles for a component."""
        return {k: v for k, v in styles.items() if v is not None}
    
    def _compile_component_variants(self, variants: Dict[str, Any]) -> Dict[str, Any]:
        """Compile variants for a component."""
        compiled = {}
        
        for variant_name, variant_config in variants.items():
            compiled_variant = {
                "modifier": variant_config.get("modifier"),
                "styles": self._compile_variant_styles(variant_config.get("styles", {}))
            }
            
            compiled[variant_name] = compiled_variant
        
        return compiled
    
    def _compile_variant_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Compile styles for a variant."""
        return {k: v for k, v in styles.items() if v is not None}
    
    def _compile_accessibility(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile accessibility settings."""
        accessibility = theme_config.get("accessibility", {})
        compiled = {
            "contrast": self._compile_contrast_settings(accessibility.get("contrast", {})),
            "keyboard": accessibility.get("keyboard", {}),
            "aria": accessibility.get("aria", {})
        }
        
        return compiled
    
    def _compile_contrast_settings(self, contrast: Dict[str, Any]) -> Dict[str, Any]:
        """Compile contrast settings with validation."""
        compiled = {}
        
        for key, value in contrast.items():
            if isinstance(value, float) and value > 0:
                compiled[key] = min(max(value, 1.0), 21.0)
            else:
                compiled[key] = value
        
        return compiled
    
    def _compile_responsive(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile responsive design settings."""
        responsive = theme_config.get("responsive", {})
        compiled = {
            "breakpoints": self._compile_breakpoints(responsive.get("breakpoints", {})),
            "queries": responsive.get("queries", {})
        }
        
        return compiled
    
    def _compile_breakpoints(self, breakpoints: Dict[str, Any]) -> Dict[str, Any]:
        """Compile breakpoint definitions."""
        compiled = {}
        
        for name, value in breakpoints.items():
            if isinstance(value, str):
                # Standardize breakpoint format
                if value.startswith(("min-", "max-")):
                    compiled[name] = value
                else:
                    compiled[name] = f"min-width: {value}"
            # Add parsing for other breakpoint formats if needed
        
        return compiled
    
    def _compile_animations(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """Compile animations section."""
        animations = theme_config.get("animations", {})
        compiled = {}
        
        for animation_name, animation_config in animations.items():
            compiled_animation = {
                "keyframes": self._compile_keyframes(animation_config.get("keyframes", {})),
                "duration": animation_config.get("duration", "0.5s"),
                "timing_function": animation_config.get("timing_function", "ease"),
                "iteration_count": animation_config.get("iteration_count", "1"),
                "direction": animation_config.get("direction", "normal")
            }
            
            compiled[animation_name] = compiled_animation
        
        return compiled
    
    def _compile_keyframes(self, keyframes: Dict[str, Any]) -> Dict[str, Any]:
        """Compile animation keyframes."""
        compiled = {}
        
        for selector, properties in keyframes.items():
            compiled_selector = {
                "properties": {k: v for k, v in properties.items() if v is not None},
                "easing": properties.get("easing")
            }
            
            compiled[selector] = compiled_selector
        
        return compiled
```

4.5 Optimizer Module

```python
# theme_builder/optimizer.py

from __future__ import annotations
import json
from typing import Dict, Any, List, Optional
from .builder import Builder
from .compiler import ThemeCompiler


class ThemeOptimizer:
    """
    Optimizes compiled theme configurations.
    Removes unused properties, merges duplicates, and compresses data.
    """
    
    def __init__(self, builder: Optional[Builder] = None):
        self.builder = builder or Builder()
        self.compiler = ThemeCompiler(builder)
    
    def optimize(self, compiled_theme: Dict[str, Any]) -> Dict[str, Any]:
        """
        Optimize a compiled theme.
        
        Args:
            compiled_theme: Compiled theme configuration
            
        Returns:
            Optimized theme configuration
        """
        optimized = {
            "metadata": compiled_theme.get("metadata", {}),
            "colors": self._optimize_colors(compiled_theme.get("colors", {})),
            "typography": self._optimize_typography(compiled_theme.get("typography", {})),
            "components": self._optimize_components(compiled_theme.get("components", {})),
            "accessibility": self._optimize_accessibility(compiled_theme.get("accessibility", {})),
            "responsive": self._optimize_responsive(compiled_theme.get("responsive", {})),
            "animations": self._optimize_animations(compiled_theme.get("animations", {}))
        }
        
        return optimized
    
    def _optimize_colors(self, colors: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize colors by removing duplicate values."""
        optimized = {}
        seen_values = set()
        
        for key, value in colors.items():
            if value not in seen_values:
                seen_values.add(value)
                optimized[key] = value
        
        return optimized
    
    def _optimize_typography(self, typography: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize typography by merging identical variants."""
        optimized = {
            "font_family": typography.get("font_family"),
            "font_size": typography.get("font_size"),
            "line_height": typography.get("line_height"),
            "letter_spacing": typography.get("letter_spacing"),
            "variants": {}
        }
        
        variants = typography.get("variants", {})
        if variants:
            optimized["variants"] = self._optimize_variants(variants)
        
        return optimized
    
    def _optimize_variants(self, variants: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize typography variants by removing duplicates."""
        optimized = {}
        seen = set()
        
        for variant_name, variant_config in variants.items():
            variant_key = (variant_config.get("font_size", ""), 
                          variant_config.get("line_height", ""), 
                          variant_config.get("font_weight", ""))
            
            if variant_key not in seen:
                seen.add(variant_key)
                optimized[variant_name] = variant_config
        
        return optimized
    
    def _optimize_components(self, components: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize component configurations."""
        optimized = {}
        
        for component_name, component_config in components.items():
            optimized_component = {
                "base": self._optimize_base_styles(component_config.get("base", {})),
                "variants": self._optimize_component_variants(
                    component_config.get("variants", {})
                )
            }
            
            optimized[component_name] = optimized_component
        
        return optimized
    
    def _optimize_base_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize base styles by removing null/empty values."""
        return {k: v for k, v in styles.items() if v is not None and v != ""}
    
    def _optimize_component_variants(self, variants: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize component variants."""
        optimized = {}
        
        for variant_name, variant_config in variants.items():
            optimized_variant = {
                "modifier": variant_config.get("modifier"),
                "styles": self._optimize_variant_styles(variant_config.get("styles", {}))
            }
            
            optimized[variant_name] = optimized_variant
        
        return optimized
    
    def _optimize_variant_styles(self, styles: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize variant styles."""
        return {k: v for k, v in styles.items() if v is not None and v != ""}
    
    def _optimize_accessibility(self, accessibility: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize accessibility settings."""
        optimized = {
            "contrast": self._optimize_contrast_settings(accessibility.get("contrast", {})),
            "keyboard": accessibility.get("keyboard", {}),
            "aria": accessibility.get("aria", {})
        }
        
        return optimized
    
    def _optimize_contrast_settings(self, contrast: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize contrast settings by rounding values."""
        optimized = {}
        
        for key, value in contrast.items():
            if isinstance(value, float):
                optimized[key] = round(value, 2)
            else:
                optimized[key] = value
        
        return optimized
    
    def _optimize_responsive(self, responsive: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize responsive settings."""
        optimized = {
            "breakpoints": self._optimize_breakpoints(responsive.get("breakpoints", {})),
            "queries": responsive.get("queries", {})
        }
        
        return optimized
    
    def _optimize_breakpoints(self, breakpoints: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize breakpoint definitions."""
        return {k: v for k, v in breakpoints.items() if v}
    
    def _optimize_animations(self, animations: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize animations section."""
        optimized = {}
        
        for animation_name, animation_config in animations.items():
            optimized_animation = {
                "keyframes": self._optimize_keyframes(animation_config.get("keyframes", {})),
                "duration": animation_config.get("duration"),
                "timing_function": animation_config.get("timing_function"),
                "iteration_count": animation_config.get("iteration_count"),
                "direction": animation_config.get("direction")
            }
            
            optimized[animation_name] = optimized_animation
        
        return optimized
    
    def _optimize_keyframes(self, keyframes: Dict[str, Any]) -> Dict[str, Any]:
        """Optimize animation keyframes."""
        optimized = {}
        
        for selector, selector_config in keyframes.items():
            optimized_selector = {
                "properties": {k: v for k, v in selector_config.get("properties", {}).items() 
                              if v is not None and v != ""},
                "easing": selector_config.get("easing")
            }
            
            if optimized_selector["properties"]:
                optimized[selector] = optimized_selector
        
        return optimized
```

4.6 Translator Module

```python
# theme_builder/translator.py

from __future__ import annotations
import json
from typing import Dict, Any, List, Optional
from .builder import Builder


class ThemeTranslator:
    """
    Handles localization and translation of theme content.
    Supports multi-language interfaces and localized text.
    """
    
    def __init__(self, builder: Optional[Builder] = None):
        self.builder = builder or Builder()
    
    def translate(self, theme_config: Dict[str, Any], language: str = "en") -> Dict[str, Any]:
        """
        Translate a theme configuration into the specified language.
        
        Args:
            theme_config: Original theme configuration
            language: Target language code (e.g., "en", "es", "fr")
            
        Returns:
            Translated theme configuration
        """
        translations = self._load_translations(language)
        
        translated = {
            "metadata": self._translate_metadata(theme_config.get("metadata", {}), translations),
            "colors": theme_config.get("colors", {}),
            "typography": self._translate_typography(theme_config.get("typography", {}), translations),
            "components": self._translate_components(theme_config.get("components", {}), translations),
            "accessibility": self._translate_accessibility(theme_config.get("accessibility", {}), translations),
            "responsive": theme_config.get("responsive", {}),
            "animations": theme_config.get("animations", {})
        }
        
        return translated
    
    def _load_translations(self, language: str) -> Dict[str, Any]:
        """Load translation strings for the specified language."""
        # This is a simple in-memory example
        # In practice, you might load from external JSON files
        translations = {
            "en": {
                "accessibility": {
                    "required_label": "Required",
                    "loading_state": "Loading...",
                    "error_state": "Error",
                    "success_state": "Success"
                },
                "components": {
                    "button": {
                        "default": "Button",
                        "cancel": "Cancel",
                        "confirm": "Confirm",
                        "close": "Close"
                    }
                },
                "validation": {
                    "required": "{field} is required",
                    "invalid_format": "{field} has invalid format"
                }
            },
            "es": {
                "accessibility": {
                    "required_label": "Requerido",
                    "loading_state": "Cargando...",
                    "error_state": "Error",
                    "success_state": "Éxito"
                },
                "components": {
                    "button": {
                        "default": "Botón",
                        "cancel": "Cancelar",
                        "confirm": "Confirmar",
                        "close": "Cerrar"
                    }
                },
                "validation": {
                    "required": "{field} es obligatorio",
                    "invalid_format": "{field} tiene formato inválido"
                }
            },
            "fr": {
                "accessibility": {
                    "required_label": "Requisé",
                    "loading_state": "Chargement...",
                    "error_state": "Erreur",
                    "success_state": "Succès"
                },
                "components": {
                    "button": {
                        "default": "Bouton",
                        "cancel": "Annuler",
                        "confirm": "Confirmer",
                        "close": "Fermer"
                    }
                },
                "validation": {
                    "required": "{field} est requis",
                    "invalid_format": "{field} a un format invalide"
                }
            }
        }
        
        return translations.get(language, translations["en"])
    
    def _translate_metadata(self, metadata: Dict[str, Any], translations: Dict[str, Any]) -> Dict[str, Any]:
        """Translate metadata section."""
        translated = {
            "name": metadata.get("name"),
            "description": self._translate_text(metadata.get("description"), translations),
            "author": metadata.get("author"),
            "version": metadata.get("version"),
            "build_id": metadata.get("build_id"),
            "timestamp": metadata.get("timestamp")
        }
        
        return translated
    
    def _translate_text(self, text: Optional[str], translations: Dict[str, Any]) -> Optional[str]:
        """Translate a text string using translations."""
        if not text:
            return text
        
        # Check if text exists in translations
        for key in text.split():
            if key in translations.get("validation", {}):
                return translations["validation"][key]
        
        return text
    
    def _translate_typography(self, typography: Dict[str, Any], translations: Dict[str, Any]) -> Dict[str, Any]:
        """Translate typography section."""
        return typography
    
    def _translate_components(self, components: Dict[str, Any], translations: Dict[str, Any]) -> Dict[str, Any]:
        """Translate components section."""
        translated = {}
        
        for component_name, component_config in components.items():
            translated_component = {
                "base": component_config.get("base", {}),
                "variants": self._translate_component_variants(
                    component_config.get("variants", {}), translations
                )
            }
            
            translated[component_name] = translated_component
        
        return translated
    
    def _translate_component_variants(self, variants: Dict[str, Any], translations: Dict[str, Any]) -> Dict[str, Any]:
        """Translate component variants."""
        translated = {}
        
        for variant_name, variant_config in variants.items():
            translated_variant = {
                "modifier": variant_config.get("modifier"),
                "styles": variant_config.get("styles", {})
            }
            
            # Translate button text if present
            if variant_name == "button":
                translated_variant["text"] = self._translate_button_text(
                    variant_config.get("text", {}), translations
                )
            
            translated[variant_name] = translated_variant
        
        return translated
    
    def _translate_button_text(self, button_text: Dict[str, Any], translations: Dict[str, Any]) -> Dict[str, Any]:
        """Translate button text properties."""
        translated = {}
        
        for key, value in button_text.items():
            translated_key = key
            if key in translations.get("components", {}).get("button", {}):
                translated_key = translations["components"]["button"][key]
            
            translated[translated_key] = value
        
        return translated
    
    def _translate_accessibility(self, accessibility: Dict[str, Any], translations: Dict[str, Any]) -> Dict[str, Any]:
        """Translate accessibility section."""
        translated = {
            "contrast": accessibility.get("contrast", {}),
            "keyboard": accessibility.get("keyboard", {}),
            "aria": {
                "label": self._translate_text(accessibility.get("aria", {}).get("label"), translations),
                "instructions": self._translate_text(accessibility.get("aria", {}).get("instructions"), translations)
            }
        }
        
        return translated
```

4.7 Exceptions Module

```python
# theme_builder/exceptions.py

from __future__ import annotations
from typing import Optional


class ThemeBuilderError(Exception):
    """Base exception class for Theme Builder errors."""
    pass


class ConfigurationError(ThemeBuilderError):
    """Raised when there's a configuration error."""
    pass


class ValidationException(ThemeBuilderError):
    """Raised when validation fails."""
    pass


class BuildFailedError(ThemeBuilderError):
    """Raised when build process fails."""
    pass


class FileNotFoundError(ThemeBuilderError):
    """Raised when a file is not found."""
    pass


class ThemeNotFoundException(ThemeBuilderError):
    """Raised when a theme is not found."""
    pass


class CompilationError(ThemeBuilderError):
    """Raised when compilation fails."""
    pass


class MinificationError(ThemeBuilderError):
    """Raised when minification fails."""
    pass


class ServingError(ThemeBuilderError):
    """Raised when serving fails."""
    pass


class InvalidThemeError(ThemeBuilderError):
    """Raised when a theme is invalid."""
    pass


class OptimizationError(ThemeBuilderError):
    """Raised when optimization fails."""
    pass


class TranslationError(ThemeBuilderError):
    """Raised when translation fails."""
    pass


class ThemeAlreadyExistsError(ThemeBuilderError):
    """Raised when trying to create an existing theme."""
    pass


class BreakpointNotFoundError(ThemeBuilderError):
    """Raised when a breakpoint is not found."""
    pass


class VariantNotFoundError(ThemeBuilderError):
    """Raised when a variant is not found."""
    pass


class ComponentNotFoundError(ThemeBuilderError):
    """Raised when a component is not found."""
    pass


class AccessibilityValidationError(ThemeBuilderError):
    """Raised when accessibility validation fails."""
    pass


class ThemeExportError(ThemeBuilderError):
    """Raised when exporting a theme fails."""
    pass


class ThemeImportError(ThemeBuilderError):
    """Raised when importing a theme fails."""
    pass


class ThemeLockError(ThemeBuilderError):
    """Raised when there's an issue with theme locking."""
    pass


class ThemeUnlockError(ThemeBuilderError):
    """Raised when there's an issue with theme unlocking."""
    pass


class ThemeVersionMismatchError(ThemeBuilderError):
    """Raised when theme versions mismatch."""
    pass


class ThemeCircularDependencyError(ThemeBuilderError):
    """Raised when a circular dependency is detected."""
    pass


class ThemeReferenceError(ThemeBuilderError):
    """Raised when a theme reference is invalid."""
    pass


class ThemeBuildWarning(Warning):
    """Warning category for theme build issues."""
    pass


class ThemeValidationWarning(Warning):
    """Warning category for theme validation issues."""
    pass


class ThemeAccessibilityWarning(Warning):
    """Warning category for accessibility issues."""
    pass


class ThemePerformanceWarning(Warning):
    """Warning category for performance issues."""
    pass


class ThemeDeprecatedWarning(Warning):
    """Warning category for deprecated features."""
    pass
```

5. Advanced Usage
-----------------

5.1 Plugin System

```python
# theme_builder/plugins.py

from __future__ import annotations
import importlib
from typing import Dict, Any, List, Optional, Callable, Type
from .builder import Builder


class PluginLoader:
    """
    Loads and manages plugins for the theme builder.
    Supports dynamic plugin discovery and registration.
    """
    
    def __init__(self, builder: Builder):
        self.builder = builder
        self.plugins: Dict[str, 'Plugin'] = {}
        self.discovered_plugins: List[str] = []
    
    def discover_plugins(self, plugin_paths: List[str]) -> List[str]:
        """
        Discover plugins in specified directories.
        
        Args:
            plugin_paths: List of directories to search for plugins
            
        Returns:
            List of discovered plugin names
        """
        self.discovered_plugins = []
        
        for path in plugin_paths:
            if os.path.exists(path):
                self._scan_directory(path)
        
        return self.discovered_plugins
    
    def _scan_directory(self, directory: str):
        """Scan a directory for plugin modules."""
        try:
            for filename in os.listdir(directory):
                if filename.endswith(".py") and filename != "__init__.py":
                    module_name = filename[:-3]
                    plugin_id = f"{directory}:{module_name}"
                    
                    if plugin_id not in self.discovered_plugins:
                        self.discovered_plugins.append(plugin_id)
        except Exception as e:
            self.builder.logger.warning(f"Error scanning directory {directory}: {str(e)}")
    
    def load_plugin(self, plugin_id: str) -> Optional['Plugin']:
        """
        Load a plugin by ID.
        
        Args:
            plugin_id: Identifier for the plugin
            
        Returns:
            Plugin instance or None if not found
        """
        try:
            module_name, class_name = plugin_id.rsplit(":", 1)
            module = importlib.import_module(module_name)
            PluginClass = getattr(module, class_name, None)
            
            if PluginClass and issubclass(PluginClass, Plugin):
                if plugin_id in self.plugins:
                    return self.plugins[plugin_id]
                
                plugin = PluginClass(self.builder)
                self.plugins[plugin_id] = plugin
                return plugin
            
            return None
        except Exception as e:
            self.builder.logger.error(f"Error loading plugin {plugin_id}: {str(e)}")
            return None
    
    def unload_plugin(self, plugin_id: str) -> bool:
        """
        Unload a plugin.
        
        Args:
            plugin_id: Plugin identifier
            
        Returns:
            True if successfully unloaded, False otherwise
        """
        if plugin_id in self.plugins:
            plugin = self.plugins.pop(plugin_id)
            plugin.unload()
            return True
        
        return False
    
    def get_plugins(self) -> Dict[str, 'Plugin']:
        """Get all loaded plugins."""
        return self.plugins.copy()


class Plugin:
    """
    Base class for theme builder plugins.
    Plugins extend functionality of the theme builder.
    """
    
    def __init__(self, builder: Builder):
        self.builder = builder
    
    def hook(self, hook_name: str, *args, **kwargs) -> Any:
        """
        Call a hook with the same name.
        
        Args:
            hook_name: Name of the hook
            *args: Positional arguments
            **kwargs: Keyword arguments
            
        Returns:
            Result of the hook execution
        """
        method = getattr(self, f"on_{hook_name}", None)
        if callable(method):
            return method(*args, **kwargs)
        
        self.builder.logger.debug(f"No hook found for {hook_name}")
        return None
    
    def unload(self):
        """Clean up plugin resources."""
        pass


class PreBuildPlugin(Plugin):
    """
    Plugin hooking into the pre-build phase.
    Can modify theme configurations before building.
    """
    
    def on_pre_build(self, themes: Dict[str, Dict[str, Any]]) -> Dict[str, Dict[str, Any]]:
        """
        Modify themes before building.
        
        Args:
            themes: Dictionary of themes to build
            
        Returns:
            Modified themes dictionary
        """
        self.builder.logger.info("Running pre-build plugins...")
        return themes


class PostBuildPlugin(Plugin):
    """
    Plugin hooking into the post-build phase.
    Can process built themes.
    """
    
    def on_post_build(self, built_themes: Dict[str, Dict[str, Any]]) -> Dict[str, Dict[str, Any]]:
        """
        Process built themes.
        
        Args:
            built_themes: Dictionary of built themes
            
        Returns:
            Modified themes dictionary
        """
        self.builder.logger.info("Running post-build plugins...")
        return built_themes


class ThemeValidationPlugin(Plugin):
    """
    Plugin for validating themes.
    Can add custom validation rules.
    """
    
    def on_validate_theme(self, theme_config: Dict[str, Any]) -> ValidationResults:
        """
        Validate a single theme.
        
        Args:
            theme_config: Theme configuration to validate
            
        Returns:
            Validation results
        """
        return ValidationResults()


class AccessibilityPlugin(Plugin):
    """
    Plugin for accessibility checks.
    Can add custom accessibility validation.
    """
    
    def on_validate_accessibility(self, accessibility_config: Dict[str, Any]) -> ValidationResults:
        """
        Validate accessibility configuration.
        
        Args:
            accessibility_config: Accessibility configuration
            
        Returns:
            Validation results
        """
        return ValidationResults()


class OptimizationPlugin(Plugin):
    """
    Plugin for optimizing themes.
    Can add custom optimization strategies.
    """
    
    def on_optimize_theme(self, compiled_theme: Dict[str, Any]) -> Dict[str, Any]:
        """
        Optimize a compiled theme.
        
        Args:
            compiled_theme: Compiled theme configuration
            
        Returns:
            Optimized theme configuration
        """
        return compiled_theme


class CompressionPlugin(Plugin):
    """
    Plugin for compressing theme output.
    Can add custom compression methods.
    """
    
    def on_compress_output(self, output_data: str) -> str:
        """
        Compress output data.
        
        Args:
            output_data: Data to compress
            
        Returns:
            Compressed data
        """
        return output_data


class MinificationPlugin(Plugin):
    """
    Plugin for minifying CSS output.
    Can add custom minification strategies.
    """
    
    def on_minify_css(self, css: str) -> str:
        """
        Minify CSS content.
        
        Args:
            css: CSS content to minify
            
        Returns:
            Minified CSS
        """
        return css


class PostProcessingPlugin(Plugin):
    """
    Plugin for post-processing built files.
    Can add custom file transformations.
    """
    
    def on_post_process_file(self, file_path: str, content: str) -> str:
        """
        Process a built file.
        
        Args:
            file_path: Path to the file
            content: File content
            
        Returns:
            Modified content
        """
        return content


class DeploymentPlugin(Plugin):
    """
    Plugin for deploying built themes.
    Can add custom deployment strategies.
    """
    
    def on_deploy(self, build_output: str, deployment_target: str):
        """
        Deploy built themes.
        
        Args:
            build_output: Path to build output
            deployment_target: Deployment target (e.g., "production", "staging")
        """
        pass


class CachePlugin(Plugin):
    """
    Plugin for caching build artifacts.
    Can add custom caching mechanisms.
    """
    
    def on_cache_build(self, build_id: str, output_data: Dict[str, Any]):
        """
        Cache build artifacts.
        
        Args:
            build_id: Identifier for this build
            output_data: Build output data
        """
        pass
    
    def on_retrieve_cache(self, build_id: str) -> Optional[Dict[str, Any]]:
        """
        Retrieve cached build artifacts.
        
        Args:
            build_id: Identifier for the build
            
        Returns:
            Cached data or None
        """
        return None


class AnalyticsPlugin(Plugin):
    """
    Plugin for collecting build analytics.
    Can add custom metrics tracking.
    """
    
    def on_track_build_metrics(self, metrics: Dict[str, Any]):
        """
        Track build metrics.
        
        Args:
            metrics: Dictionary of build metrics
        """
        pass


class NotificationPlugin(Plugin):
    """
    Plugin for sending build notifications.
    Can add custom notification systems.
    """
    
    def on_notify_build_completion(self, success: bool, build_id: str):
        """
        Notify about build completion.
        
        Args:
            success: Whether the build succeeded
            build_id: Build identifier
        """
        pass


class LoggingPlugin(Plugin):
    """
    Plugin for custom logging.
    Can add advanced logging capabilities.
    """
    
    def on_log_message(self, level: str, message: str, context: Dict[str, Any]):
        """
        Handle a log message.
        
        Args:
            level: Log level (info, warning, error, debug)
            message: Log message
            context: Additional context
        """
        pass


class CacheControlPlugin(Plugin):
    """
    Plugin for managing cache control headers.
    Can add custom caching strategies for built files.
    """
    
    def on_set_cache_headers(self, file_path: str) -> Dict[str, str]:
        """
        Set cache control headers for a file.
        
        Args:
            file_path: Path to the file
            
        Returns:
            Dictionary of cache control headers
        """
        return {
            "Cache-Control": "max-age=31536000, immutable"
        }


class SecurityPlugin(Plugin):
    """
    Plugin for security-related processing.
    Can add custom security checks and transformations.
    """
    
    def on_sanitize_output(self, output_data: Dict[str, Any]) -> Dict[str, Any]:
        """
        Sanitize build output.
        
        Args:
            output_data: Build output data
            
        Returns:
            Sanitized data
        """
        return output_data
    
    def on_validate_integrity(self, output_data: Dict[str, Any]) -> bool:
        """
        Validate output integrity.
        
        Args:
            output_data: Build output data
            
        Returns:
            True if valid, False otherwise
        """
        return True


class PerformancePlugin(Plugin):
    """
    Plugin for performance optimization.
    Can add custom performance improvements.
    """
    
    def on_optimize_for_performance(self, compiled_theme: Dict[str, Any]) -> Dict[str, Any]:
        """
        Optimize theme for performance.
        
        Args:
            compiled_theme: Compiled theme configuration
            
        Returns:
            Optimized theme configuration
        """
        return compiled_theme


class LocalizationPlugin(Plugin):
    """
    Plugin for localization and internationalization.
    Can add custom translation strategies.
    """
    
    def on_translate_theme(self, theme_config: Dict[str, Any], language: str) -> Dict[str, Any]:
        """
        Translate theme configuration.
        
        Args:
            theme_config: Original theme configuration
            language: Target language
            
        Returns:
            Translated theme configuration
        """
        return theme_config


class VariantPlugin(Plugin):
    """
    Plugin for managing component variants.
    Can add custom variant processing.
    """
    
    def on_process_variants(self, components: Dict[str, Any]) -> Dict[str, Any]:
        """
        Process component variants.
        
        Args:
            components: Component configurations
            
        Returns:
            Processed components with variants
        """
        return components


class BreakpointPlugin(Plugin):
    """
    Plugin for managing responsive breakpoints.
    Can add custom breakpoint processing.
    """
    
    def on_process_breakpoints(self, breakpoints: Dict[str, Any]) -> Dict[str, Any]:
        """
        Process breakpoints.
        
        Args:
            breakpoints: Breakpoint configurations
            
        Returns:
            Processsystematically processed breakpoints
        """
        return breakpoints


class AnimationPlugin(Plugin):
    """
    Plugin for managing animations.
    Can add custom animation processing.
    """
    
    def on_process_animations(self, animations: Dict[str, Any]) -> Dict[str, Any]:
        """
        Process animations.
        
        Args:
            animations: Animation configurations
            
        Returns:
            Processed animations
        """
        return animations


class ThemeReferencePlugin(Plugin):
    """
    Plugin for managing theme references and imports.
    Can add custom reference resolution.
    """
    
    def on_resolve_references(self, theme_config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Resolve theme references.
        
        Args:
            theme_config: Theme configuration with references
            
        Returns:
            Theme configuration with resolved references
        """
        return theme_config


class ConfigurationPlugin(Plugin):
    """
    Plugin for managing configuration processing.
    Can add custom configuration transformations.
    """
    
    def on_process_configuration(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Process builder configuration.
        
        Args:
            config: Builder configuration
            
        Returns:
            Processed configuration
        """
        return config


class EnvironmentPlugin(Plugin):
    """
    Plugin for environment-specific processing.
    Can add custom environment transformations.
    """
    
    def on_apply_environment_settings(self, theme_config: Dict[str, Any], environment: str) -> Dict[str, Any]:
        """
        Apply environment-specific settings.
        
        Args:
            theme_config: Theme configuration
            environment: Current environment (development, staging, production)
            
        Returns:
            Configuration with environment settings applied
        """
        return theme_config


class SecurityHardeningPlugin(Plugin):
    """
    Plugin for security hardening.
    Can add additional security measures.
    """
    
    def on_harden_security(self, build_output: str) -> str:
        """
        Harden security of build output.
        
        Args:
            build_output: Build output content
            
        Returns:
            Security-hardened content
        """
        return build_output


class ContentSecurityPlugin(Plugin):
    """
    Plugin for Content Security Policy (CSP) handling.
    Can add custom CSP directives.
    """
    
    def on_generate_csp_headers(self) -> Dict[str, str]:
        """
        Generate Content Security Policy headers.
        
        Returns:
            Dictionary of CSP headers
        """
        return {
            "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline'; "
                                         "style-src 'self' 'unsafe-inline'; "
                                         "img-src 'self' data:; "
                                         "font-src 'self'; "
                                         "connect-src 'self'; "
                                         "frame-src 'none'; "
                                         "form-action 'self'; "
                                         "frame-ancestors 'self'; "
                                         "upgrade-insecure-requests"
        }


class CorsPlugin(Plugin):
    """
    Plugin for managing Cross-Origin Resource Sharing (CORS).
    Can add custom CORS configurations.
    """
    
    def on_set_cors_headers(self) -> Dict[str, str]:
        """
        Set CORS headers.
        
        Returns:
            Dictionary of CORS headers
        """
        return {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
            "Access-Control-Allow-Headers": "Content-Type, Authorization",
            "Access-Control-Allow-Credentials": "true",
            "Access-Control-Max-Age": "86400"
        }


class HttpSecurityPlugin(Plugin):
    """
    Plugin for HTTP security headers.
    Can add custom security headers.
    """
    
    def on_set_security_headers(self) -> Dict[str, str]:
        """
        Set HTTP security headers.
        
        Returns:
            Dictionary of security headers
        """
        return {
            "X-Content-Type-Options": "nosniff",
            "X-Frame-Options": "DENY",
            "X-XSS-Protection": "1; mode=block",
            "Strict-Transport-Security": "max-age=31536000; includeSubDomains"
        }


class PerformanceOptimizationPlugin(Plugin):
    """
    Plugin for performance optimization.
    Can add advanced performance improvements.
    """
    
    def on_optimize_resources(self, resources: Dict[str, str]) -> Dict[str, str]:
        """
        Optimize static resources.
        
        Args:
            resources: Dictionary of resource paths and contents
            
        Returns:
            Optimized resources
        """
        optimized = {}
        
        for resource_id, content in resources.items():
            if resource_id.endswith(".css"):
                optimized[resource_id] = self._optimize_css(content)
            elif resource_id.endswith(".js"):
                optimized[resource_id] = self._optimize_js(content)
            else:
                optimized[resource_id] = content
        
        return optimized
    
    def _optimize_css(self, css: str) -> str:
        """Optimize CSS content."""
        # This is a simple example - use a proper CSS optimizer in practice
        return css
    
    def _optimize_js(self, js: str) -> str:
        """Optimize JavaScript content."""
        # This is a simple example - use a proper JS optimizer in practice
        return js


class TreeShakingPlugin(Plugin):
    """
    Plugin for tree shaking - removing unused code.
    Particularly useful for JavaScript/CSS.
    """
    
    def on_perform_tree_shaking(self, code: str, dependencies: Dict[str, Any]) -> str:
        """
        Perform tree shaking on code.
        
        Args:
            code: Code to analyze
            dependencies: Dependency graph
            
        Returns:
            Trimmed code with only used portions
        """
        # This is a conceptual implementation
        # Actual tree shaking requires detailed analysis
        return code


class DeadCodeEliminationPlugin(Plugin):
    """
    Plugin for removing dead/unused code.
    """
    
    def on_remove_dead_code(self, code: str) -> str:
        """
        Remove dead code from content.
        
        Args:
            code: Code to process
            
        Returns:
            Code with dead portions removed
        """
        # Simple example - remove empty functions and comments
        lines = []
        in_comment = False
        
        for line in code.split("\n"):
            stripped = line.strip()
            
            if in_comment:
                if "*/" in stripped:
                    in_comment = False
                    lines.append(stripped)
                continue
                
            if "/*" in stripped or "//" in stripped:
                in_comment = True
                lines.append(stripped)
                continue
                
            if stripped:
                lines.append(stripped)
        
        return "\n".join(lines)


class CodeMinificationPlugin(Plugin):
    """
    Plugin for code minification.
    """
    
    def on_minify_code(self, code: str, content_type: str) -> str:
        """
        Minify code based on content type.
        
        Args:
            code: Code to minify
            content_type: Type of code (css, js, html)
            
        Returns:
            Minified code
        """
        if content_type == "css":
            return self._minify_css(code)
        elif content_type == "js":
            return self._minify_js(code)
        elif content_type == "html":
            return self._minify_html(code)
        return code
    
    def _minify_css(self, css: str) -> str:
        """Minify CSS content."""
        import re
        # Remove comments
        css = re.sub(r"/\*.*?\*/", "", css, flags=re.DOTALL)
        # Remove whitespace
        css = re.sub(r"\s+", " ", css)
        return css.strip()
    
    def _minify_js(self, js: str) -> str:
        """Minify JavaScript content."""
        import re
        # Remove comments
        js = re.sub(r"(//.*?$|\$.*)", "", js, flags=re.DOTALL)
        # Remove whitespace
        js = re.sub(r"\s+", " ", js)
        return js.strip()
    
    def _minify_html(self, html: str) -> str:
        """Minify HTML content."""
        import re
        # Remove comments
        html = re.sub(r"<!--.*?-->", "", html, flags=re.DOTALL)
        # Remove whitespace from HTML elements
        html = re.sub(r">\s+<', '>', html)
        return html.strip()


class ImageOptimizationPlugin(Plugin):
    """
    Plugin for optimizing images.
    """
    
    def on_optimize_images(self, image_paths: List[str]) -> Dict[str, str]:
        """
        Optimize images.
        
        Args:
            image_paths: List of image file paths
            
        Returns:
            Dictionary of optimized images
        """
        optimized = {}
        
        for path in image_paths:
            # Simple optimization - in practice use image processing library
            optimized[path] = path  # This would actually process and return optimized path
        
        return optimized


class FontOptimizationPlugin(Plugin):
    """
    Plugin for optimizing fonts.
    """
    
    def on_optimize_fonts(self, font_paths: List[str]) -> Dict[str, str]:
        """
        Optimize fonts.
        
        Args:
            font_paths: List of font file paths
            
        Returns:
            Dictionary of optimized fonts
        """
        optimized = {}
        
        for path in font_paths:
            # Font optimization logic
            optimized[path] = path
        
        return optimized


class AssetHashingPlugin(Plugin):
    """
    Plugin for generating content hashes for assets.
    Helps with caching and content validation.
    """
    
    def on_generate_asset_hashes(self, assets: Dict[str, str]) -> Dict[str, str]:
        """
        Generate hashes for assets.
        
        Args:
            assets: Dictionary of asset contents
            
        Returns:
            Dictionary of asset hashes
        """
        hashes = {}
        
        for asset_id, content in assets.items():
            # Using SHA-1 for shorter hashes (20 characters)
            hash_value = self._generate_hash(content)
            hashes[asset_id] = hash_value
        
        return hashes
    
    def _generate_hash(self, content: str) -> str:
        """Generate SHA-1 hash for content."""
        import hashlib
        return hashlib.sha1(content.encode()).hexdigest()


class VersioningPlugin(Plugin):
    """
    Plugin for managing theme versions.
    """
    
    def on_generate_version(self, theme_config: Dict[str, Any]) -> str:
        """
        Generate version string for theme.
        
        Args:
            theme_config: Theme configuration
            
        Returns:
            Version string
        """
        import datetime
        return f"{theme_config.get('version', '1.0.0')}-{datetime.datetime.now().strftime('%Y%m%d')}"
    
    def on_compare_versions(self, version1: str, version2: str) -> int:
        """
        Compare two version strings.
        
        Args:
            version1: First version string
            version2: Second version string
            
        Returns:
            -1 if version1 < version2, 0 if equal, 1 if version1 > version2
        """
        # Simple version comparison
        v1_parts = list(map(int, version1.split('.')))
        v2_parts = list(map(int, version2.split('.')))
        
        for i in range(max(len(v1_parts), len(v2_parts))):
            v1 = v1_parts[i] if i < len(v1_parts) else 0
            v2 = v2_parts[i] if i < len(v2_parts) else 0
            
            if v1 < v2:
                return -1
            elif v1 > v2:
                return 1
        
        return 0


class LockfilePlugin(Plugin):
    """
    Plugin for managing theme lockfiles.
    Tracks exact versions of dependencies.
    """
    
    def on_generate_lockfile(self, dependencies: Dict[str, str]) -> str:
        """
        Generate a lockfile for dependencies.
        
        Args:
            dependencies: Dictionary of dependency names and versions
            
        Returns:
            Lockfile content as string
        """
        lockfile = "# Theme Dependencies Lockfile\n# Generated by Theme Builder\n\n"
        
        for dep, version in sorted(dependencies.items()):
            lockfile += f"{dep}={version}\n"
        
        return lockfile
    
    def on_read_lockfile(self, lockfile_content: str) -> Dict[str, str]:
        """
        Parse a lockfile.
        
        Args:
            lockfile_content: Lockfile content
            
        Returns:
            Dictionary of dependencies and versions
        """
        dependencies = {}
        for line in lockfile_content.split("\n"):
            if "=" in line:
                parts = line.split("=", 1)
                if len(parts) == 2:
                    dependencies[parts[0].strip()] = parts[1].strip()
        
        return dependencies


class DependencyPlugin(Plugin):
    """
    Plugin for managing theme dependencies.
    """
    
    def on_resolve_dependencies(self, theme_config: Dict[str, Any]) -> Dict[str, str]:
        """
        Resolve dependencies for a theme.
        
        Args:
            theme_config: Theme configuration with dependencies
            
        Returns:
            Dictionary of resolved dependencies
        """
        dependencies = theme_config.get("dependencies", {})
        resolved = {}
        
        for dep_name, dep_version in dependencies.items():
            # In practice, this would actually resolve the dependency
            resolved[dep_name] = f"{dep_name}@{dep_version}"
        
        return resolved
    
    def on_install_dependencies(self, dependencies: Dict[str, str]) -> bool:
        """
        Install dependencies.
        
        Args:
            dependencies: Dictionary of dependencies to install
            
        Returns:
            True if successful, False otherwise
        """
        # This is a conceptual implementation
        # Actual installation would require package management integration
        return True


class PackageManagerPlugin(Plugin):
    """
    Plugin for interacting with package managers.
    """
    
    def on_install_packages(self, packages: List[str]) -> bool:
        """
        Install packages using the package manager.
        
        Args:
            packages: List of package names to install
            
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info(f"Installing packages: {', '.join(packages)}")
        return True
    
    def on_update_packages(self) -> bool:
        """
        Update all installed packages.
        
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info("Updating all packages")
        return True
    
    def on_uninstall_package(self, package_name: str) -> bool:
        """
        Uninstall a package.
        
        Args:
            package_name: Name of the package to uninstall
            
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info(f"Uninstalling package: {package_name}")
        return True


class NpmPlugin(PackageManagerPlugin):
    """
    Plugin for interacting with npm.
    """
    
    def on_install_packages(self, packages: List[str]) -> bool:
        """
        Install npm packages.
        
        Args:
            packages: List of npm package names
            
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info(f"Installing npm packages: {', '.join(packages)}")
        # In practice, execute npm install command
        return True
    
    def on_run_script(self, script_name: str) -> bool:
        """
        Run an npm script.
        
        Args:
            script_name: Name of the script to run
            
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info(f"Running npm script: {script_name}")
        return True


class YarnPlugin(PackageManagerPlugin):
    """
    Plugin for interacting with Yarn.
    """
    
    def on_install_packages(self, packages: List[str]) -> bool:
        """
        Install Yarn packages.
        
        Args:
            packages: List of package names
            
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info(f"Installing Yarn packages: {', '.join(packages)}")
        return True


class PnpmPlugin(PackageManagerPlugin):
    """
    Plugin for interacting with pnpm.
    """
    
    def on_install_packages(self, packages: List[str]) -> bool:
        """
        Install pnpm packages.
        
        Args:
            packages: List of package names
            
        Returns:
            True if successful, False otherwise
        """
        self.builder.logger.info(f"Installing pnpm packages: {', '.join(packages)}")
        return True


class PackageManagerFactory:
    """
    Factory for creating package manager plugins.
    """
    
    @staticmethod
    def create_plugin(builder: Builder) -> PackageManagerPlugin:
        """
        Create the appropriate package manager plugin.
        
        Args:
            builder: Theme builder instance
            
        Returns:
            PackageManagerPlugin instance
        """
        # In practice, this would detect the package manager in use
        # For demonstration, we'll return NpmPlugin
        return NpmPlugin(builder)
```

5.2 Configuration Management

```python
# theme_builder/config_manager.py

from __future__ import annotations
import json
import os
from typing import Dict, Any, List, Optional
from dataclasses import dataclass, field
from .builder import Builder
from .exceptions import ConfigurationError


@dataclass
class Configurable:
    """
    Base class for objects that can be configured.
    Provides configuration management functionality.
    """
    
    config: Dict[str, Any] = field(default_factory=dict)
    
    def configure(self, config: Dict[str, Any]):
        """
        Configure the object with new settings.
        
        Args:
            config: Configuration dictionary
        """
        self.config = config
    
    def get_config(self, key: str, default: Any = None) -> Any:
        """
        Get a configuration value.
        
        Args:
            key: Configuration key
            default: Default value if key not found
            
        Returns:
            Configuration value
        """
        return self.config.get(key, default)
    
    def set_config(self, key: str, value: Any):
        """
        Set a configuration value.
        
        Args:
            key: Configuration key
            value: Configuration value
        """
        self.config[key] = value


class ConfigManager(Configurable):
    """
    Manages configuration for the theme builder.
    Supports multiple configuration sources and validation.
    """
    
    def __init__(self, builder: Builder):
        super().__init__()
        self.builder = builder
        self.sources: List[Dict[str, Any]] = []
        self.loaded_config: Dict[str, Any] = {}
        self.validators: List[Callable[[Dict[str, Any]], bool]] = []
    
    def add_source(self, source: Dict[str, Any], priority: int = 0):
        """
        Add a configuration source.
        
        Args:
            source: Configuration dictionary
            priority: Priority for this source (higher = overrides lower)
        """
        self.sources.append({"source": source, "priority": priority})
        self.sources.sort(key=lambda x: x["priority"], reverse=True)
    
    def load(self):
        """
        Load and merge all configuration sources.
        """
        self.loaded_config = {}
        
        for source_info in self.sources:
            source = source_info["source"]
            self.loaded_config = self._merge_configs(self.loaded_config, source)
        
        self._validate_config()
    
    def _merge_configs(self, base: Dict[str, Any], override: Dict[str, Any]) -> Dict[str, Any]:
        """
        Merge two configuration dictionaries, with override taking precedence.
        
        Args:
            base: Base configuration
            override: Configuration to merge in
            
        Returns:
            Merged configuration
        """
        merged = base.copy()
        
        for key, value in override.items():
            if isinstance(value, dict) and key in merged and isinstance(merged[key], dict):
                merged[key] = self._merge_configs(merged[key], value)
            else:
                merged[key] = value
        
        return merged
    
    def _validate_config(self) -> bool:
        """
        Validate the merged configuration.
        
        Returns:
            True if valid, False otherwise
        """
        for validator in self.validators:
            if not validator(self.loaded_config):
                raise ConfigurationError("Configuration validation failed")
        
        return True
    
    def get(self, key: str, default: Any = None) -> Any:
        """
        Get a configuration value.
        
        Args:
            key: Configuration key
            default: Default value
            
        Returns:
            Configuration value
        """
        return self.loaded_config.get(key, default)
    
    def set(self, key: str, value: Any):
        """
        Set a configuration value.
        
        Args:
            key: Configuration key
            value: Configuration value
        """
        self.loaded_config[key] = value
    
    def save(self, file_path: str):
        """
        Save configuration to a file.
        
        Args:
            file_path: Path to save configuration
        """
        try:
            with open(file_path, "w") as f:
                json.dump(self.loaded_config, f, indent=4)
        except Exception as e:
            self.builder.logger.error(f"Failed to save configuration: {str(e)}")
    
    def load_from_file(self, file_path: str):
        """
        Load configuration from a file.
        
        Args:
            file_path: Path to configuration file
        """
        try:
            with open(file_path, "r") as f:
                config = json.load(f)
            self.add_source(config)
        except Exception as e:
            self.builder.logger.error(f"Failed to load configuration from {file_path}: {str(e)}")


class BuilderConfig(ConfigManager):
    """
    Configuration manager specifically for the Builder.
    Defines default configuration and validation rules.
    """
    
    DEFAULT_CONFIG = {
        "build": {
            "output_dir": "dist",
            "cache_dir": ".cache",
            "build_id": "default-build",
            "environment": "development",
            "verbose": False
        },
        "theme": {
            "default": "main",
            "themes_dir": "themes",
            "cache_themes": True
        },
        "accessibility": {
            "enforce_contrast": True,
            "min_contrast_ratio": 4.5
        },
        "optimization": {
            "minify_css": True,
            "minify_js": True,
            "optimize_images": True,
            "remove_whitespace": True
        },
        "deployment": {
            "auto_deploy": False,
            "deployment_target": "local"
        }
    }
    
    def __init__(self, builder: Builder):
        super().__init__(builder)
        self._setup_default_config()
        self._setup_validators()
    
    def _setup_default_config(self):
        """Set up default configuration."""
        self.add_source(self.DEFAULT_CONFIG, priority=100)
    
    def _setup_validators(self):
        """Set up configuration validators."""
        self.validators = [
            self._validate_build_section,
            self._validate_theme_section,
            self._validate_accessibility_section,
            self._validate_optimization_section,
            self._validate_deployment_section
        ]
    
    def _validate_build_section(self, config: Dict[str, Any]) -> bool:
        """Validate the build section of configuration."""
        build_config = config.get("build", {})
        
        # Validate output directory
        if not isinstance(build_config.get("output_dir"), str):
            self.builder.logger.warning("Invalid output_dir configuration")
            return False
        
        # Validate cache directory
        if not isinstance(build_config.get("cache_dir"), str):
            self.builder.logger.warning("Invalid cache_dir configuration")
            return False
        
        return True
    
    def _validate_theme_section(self, config: Dict[str, Any]) -> bool:
        """Validate the theme section of configuration."""
        theme_config = config.get("theme", {})
        
        # Validate themes directory
        if not isinstance(theme_config.get("themes_dir"), str):
            self.builder.logger.warning("Invalid themes_dir configuration")
            return False
        
        return True
    
    def _validate_accessibility_section(self, config: Dict[str, Any]) -> bool:
        """Validate the accessibility section of configuration."""
        accessibility_config = config.get("accessibility", {})
        
        # Validate contrast ratio
        min_ratio = accessibility_config.get("min_contrast_ratio", 4.5)
        if not isinstance(min_ratio, (int, float)) or min_ratio < 1.0:
            self.builder.logger.warning(f"Invalid min_contrast_ratio: {min_ratio}")
            return False
        
        return True
    
    def _validate_optimization_section(self, config: Dict[str, Any]) -> bool:
        """Validate the optimization section of configuration."""
        optimization_config = config.get("optimization", {})
        
        # Validate optimization settings
        for key in ["minify_css", "minify_js", "optimize_images"]:
            if not isinstance(optimization_config.get(key), bool):
                self.builder.logger.warning(f"Invalid {key} configuration")
                return False
        
        return True
    
    def _validate_deployment_section(self, config: Dict[str, Any]) -> bool:
        """Validate the deployment section of configuration."""
        deployment_config = config.get("deployment", {})
        
        # Validate deployment target
        if deployment_config.get("auto_deploy", False) and not isinstance(deployment_config.get("deployment_target"), str):
            self.builder.logger.warning("Invalid deployment_target configuration")
            return False
        
        return True


class EnvironmentConfig(ConfigManager):
    """
    Manages environment-specific configuration.
    Loads configuration based on current environment.
    """
    
    ENVIRONMENTS = ["development", "staging", "production", "test"]
    
    def __init__(self, builder: Builder):
        super().__init__(builder)
        self.environment = self._detect_environment()
        self.config_file = f"config/{self.environment}.json"
        
        self.add_source({"environment": self.environment}, priority=200)
        self.load_from_file(self.config_file)


class ConfigurationPlugin(Plugin):
    """
    Plugin for managing configuration.
    Can add custom configuration validation and transformation.
    """
    
    def on_validate_config(self, config: Dict[str, Any]) -> bool:
        """
        Validate configuration.
        
        Args:
            config: Configuration to validate
            
        Returns:
            True if valid, False otherwise
        """
        return True
    
    def on_transform_config(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Transform configuration.
        
        Args:
            config: Original configuration
            
        Returns:
            Transformed configuration
        """
        return config


class ConfigValidationPlugin(ConfigurationPlugin):
    """
    Plugin for advanced configuration validation.
    Adds additional validation rules.
    """
    
    def on_validate_config(self, config: Dict[str, Any]) -> bool:
        """
        Validate configuration with additional rules.
        
        Args:
            config: Configuration to validate
            
        Returns:
            True if valid, False otherwise
        """
        # Check for required configuration
        required_keys = ["build", "theme"]
        for key in required_keys:
            if key not in config:
                self.builder.logger.error(f"Missing required configuration: {key}")
                return False
        
        # Validate build configuration
        build_config = config.get("build", {})
        if "output_dir" not in build_config:
            self.builder.logger.error("Missing build.output_dir configuration")
            return False
        
        if "cache_dir" not in build_config:
            self.builder.logger.error("Missing build.cache_dir configuration")
            return False
        
        return True


class ConfigTransformationPlugin(ConfigurationPlugin):
    """
    Plugin for transforming configuration.
    Can modify configuration before use.
    """
    
    def on_transform_config(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Transform configuration.
        
        Args:
            config: Original configuration
            
        Returns:
            Transformed configuration
        """
        # Example: Convert boolean strings to actual booleans
        transformed = config.copy()
        
        for key, value in transformed.items():
            if isinstance(value, str):
                if value.lower() in ["true", "yes", "1"]:
                    transformed[key] = True
                elif value.lower() in ["false", "no", "0"]:
                    transformed[key] = False
        
        return transformed


class ConfigEnvironmentPlugin(ConfigurationPlugin):
    """
    Plugin for environment-based configuration.
    Loads environment-specific settings.
    """
    
    def on_transform_config(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """
        Transform configuration based on environment.
        
        Args:
            config: Original configuration
            
        Returns:
            Environment-transformed configuration
        """
        environment = config.get("environment", "development")
        transformed = config.copy()
        
        # Environment-specific overrides
        if environment == "production":
            transformed["build"]["output_dir"] = "dist-production"
            transformed["accessibility"]["enforce_contrast"] = True
            transformed["optimization"]["minify_css"] = True
            transformed["optimization"]["minify_js"] = True
        elif environment == "development":
            transformed["build"]["output_dir"] = "dist-development"
            transformed["accessibility"]["enforce_contrast"] = False
            transformed["optimization"]["minify_css"] = False
            transformed["optimization"]["minify_js"] = False
        
        return transformed
```

5.3 Logging and Monitoring

```python
# theme_builder/logging.py

from __future__ import annotations
import logging
import sys
import traceback
from typing import Any, Dict, List, Optional
from dataclasses import dataclass


@dataclass
class LogEntry:
    """
    Represents a log entry with structured data.
    """
    level: int
    message: str
    logger: str
    module: str
    line_number: Optional[int] = None
    stack_trace: Optional[str] = None
    extra: Dict[str, Any] = field(default_factory=dict)


class Logger:
    """
    Custom logger with enhanced functionality.
    Provides structured logging and multiple output destinations.
    """
    
    LEVELS = {
        "debug": logging.DEBUG,
        "info": logging.INFO,
        "warning": logging.WARNING,
        "error": logging.ERROR,
        "critical": logging.CRITICAL
    }
    
    def __init__(self, name: str):
        self.name = name
        self.logger = logging.getLogger(name)
        self.logger.setLevel(logging.DEBUG)
        
        # Create formatter
        self.formatter = logging.Formatter(
            '[%(asctime)s] [%(levelname)s] [%(name)s] %(message)s',
            datefmt='%Y-%m-%d %H:%M:%S'
        )
        
        # Console handler
        self.console_handler = logging.StreamHandler(sys.stdout)
        self.console_handler.setLevel(logging.INFO)
        self.console_handler.setFormatter(self.formatter)
        self.logger.addHandler(self.console_handler)
        
        # File handler (optional)
        self.file_handler: Optional[logging.FileHandler] = None
    
    def add_file_handler(self, file_path: str, level: int = logging.INFO):
        """
        Add a file handler for logging to disk.
        
        Args:
            file_path: Path to log file
            level: Logging level for file handler
        """
        if not self.file_handler:
            self.file_handler = logging.FileHandler(file_path)
            self.file_handler.setLevel(level)
            self.file_handler.setFormatter(self.formatter)
            self.logger.addHandler(self.file_handler)
    
    def remove_file_handler(self):
        """
        Remove the file handler if it exists.
        """
        if self.file_handler:
            self.logger.removeHandler(self.file_handler)
            self.file_handler = None
    
    def debug(self, message: str, *args, **kwargs):
        """Log a debug message."""
        self._log(logging.DEBUG, message, *args, **kwargs)
    
    def info(self, message: str, *args, **kwargs):
        """Log an informational message."""
        self._log(logging.INFO, message, *args, **kwargs)
    
    def warning(self, message: str, *args, **kwargs):
        """Log a warning message."""
        self._log(logging.WARNING, message, *args, **kwargs)
    
    def error(self, message: str, *args, **kwargs):
        """Log an error message."""
        self._log(logging.ERROR, message, *args, **kwargs)
    
    def critical(self, message: str, *args, **kwargs):
        """Log a critical/error message."""
        self._log(logging.CRITICAL, message, *args, **kwargs)
    
    def _log(self, level: int, message: str, *args, **kwargs):
        """
        Internal logging method.
        """
        try:
            # Get the frame information
            frame_info = self._get_frame_info(2)
            
            if frame_info:
                log_entry = LogEntry(
                    level=level,
                    message=message % args if args else message,
                    logger=self.name,
                    module=frame_info["module"],
                    line_number=frame_info["line_number"],
                    extra=kwargs
                )
                
                # Convert log entry to string
                log_message = self._format_log_entry(log_entry)
                
                # Use the standard logger
                self.logger.log(level, log_message)
            else:
                self.logger.log(level, message % args if args else message)
        except Exception as e:
            # If logging fails, fallback to basic output
            print(f"[ERROR] Failed to log: {str(e)}", file=sys.stderr)
    
    def _get_frame_info(self, stack_level: int = 2) -> Optional[Dict[str, Any]]:
        """
        Get frame information from the stack.
        """
        import inspect
        
        frame = inspect.currentframe()
        for _ in range(stack_level):
            frame = frame.f_back
        
        if not frame:
            return None
        
        module = inspect.getmodule(frame)
        module_name = module.__name__ if module else "unknown"
        
        return {
            "module": module_name,
            "line_number": frame.f_lineno
        }
    
    def _format_log_entry(self, entry: LogEntry) -> str:
        """
        Format a log entry as a string.
        """
        message = entry.message
        if entry.extra:
            message += " " + " ".join(f"{k}={v}" for k, v in entry.extra.items())
        
        return message
    
    def exception(self, message: str = "Exception occurred", *args, **kwargs):
        """
        Log an exception with stack trace.
        """
        exc_info = sys.exc_info()
        if exc_info:
            self._log(logging.ERROR, f"{message}\n{traceback.format_exc()}", *args, **kwargs)
        else:
            self._log(logging.ERROR, message, *args, **kwargs)


class StructuredLogger(Logger):
    """
    Logger that supports structured JSON logging.
    Useful for machine-readable log analysis.
    """
    
    def __init__(self, name: str):
        super().__init__(name)
        self.formatter = logging.Formatter(json.dumps({
            "timestamp": "%(asctime)s",
            "level": "%(levelname)s",
            "name": "%(name)s",
            "message": "%(message)s",
            "module": "%(module)s",
            "lineno": "%(lineno)d"
        }), datefmt='%Y-%m-%d %H:%M:%S')
        
        # Remove the console handler and add a new one
        self.logger.handlers = []
        self.console_handler = logging.StreamHandler(sys.stdout)
        self.console_handler.setFormatter(self.formatter)
        self.logger.addHandler(self.console_handler)


class LoggingPlugin(Plugin):
    """
    Plugin for managing logging.
    Can add custom logging handlers and configurations.
    """
    
    def on_configure_logging(self, logger: Logger):
        """
        Configure logging.
        
        Args:
            logger: Logger instance to configure
        """
        # Add additional handlers or modify existing ones
        pass
    
    def on_add_handler(self, logger: Logger, handler: logging.Handler):
        """
        Add a logging handler.
        
        Args:
            logger: Logger instance
            handler: Handler to add
        """
        logger.logger.addHandler(handler)
    
    def on_remove_handler(self, logger: Logger, handler: logging.Handler):
        """
        Remove a logging handler.
        
        Args:
            logger: Logger instance
            handler: Handler to remove
        """
        logger.logger.removeHandler(handler)


class FileRotationHandler(logging.Handler):
    """
    File handler that rotates log files when they grow too large.
    """
    
    def __init__(self, filename: str, max_bytes: int =10 * 1024 * 1024, backup_count: int =5):
        super().__init__()
        self.filename = filename
        self.max_bytes = max_bytes
        self.backup_count = backup_count
        self.file = None
        self.lock = threading.Lock()
        
        self._ensure_file_exists()
    
    def _ensure_file_exists(self):
        """Ensure the log file exists and is writable."""
        try:
            if not os.path.exists(self.filename):
                with open(self.filename, "w"):
                    pass
        except Exception as e:
            print(f"Failed to create log file {self.filename}: {str(e)}", file=sys.stderr)
    
    def _rotate_files(self):
        """Rotate log files if needed."""
        with self.lock:
            try:
                if self.file:
                    self.file.close()
                    self.file = None
                
                # Move existing files
                for i in range(self.backup_count-1, 0, -1):
                    src = f"{self.filename}.{i}"
                    dst = f"{self.filename}.{i+1}"
                    if os.path.exists(src):
                        os.rename(src, dst)
                
                # Rename current file
                if os.path.exists(self.filename):
                    os.rename(self.filename, f"{self.filename}.1")
                
                # Create new file
                self.file = open(self.filename, "w", encoding="utf-8")
            except Exception as e:
                print(f"Failed to rotate log files: {str(e)}", file=sys.stderr)
    
    def _is_rotation_needed(self) -> bool:
        """Check if log file rotation is needed."""
        if not self.file:
            return False
        
        try:
            self.file.seek(0, os.SEEK_END)
            size = self.file.tell()
            return size > self.max_bytes
        except Exception:
            return False
    
    def emit(self, record: logging.LogRecord):
        """
        Emit a log record.
        """
        try:
            if self._is_rotation_needed():
                self._rotate_files()
            
            if not self.file:
                self._ensure_file_exists()
                self.file = open(self.filename, "a", encoding="utf-8")
            
            message = self.format(record)
            self.file.write(message + "\n")
            self.file.flush()
        except Exception as e:
            print(f"Failed to write log: {str(e)}", file=sys.stderr)


class RateLimitedLogger(Logger):
    """
    Logger that limits the rate of log messages.
    Helps prevent log flooding.
    """
    
    def __init__(self, name: str, max_rate: float =10.0):
        super().__init__(name)
        self.max_rate = max_rate
        self.tokens = max_rate
        self.last_check = time.time()
        self.lock = threading.Lock()
    
    def _can_log(self) -> bool:
        """
        Check if we can log a message based on rate limit.
        """
        with self.lock:
            now = time.time()
            elapsed = now - self.last_check
            self.last_check = now
            
            self.tokens = min(self.max_rate, self.tokens + elapsed)
            
            if self.tokens >= 1:
                self.tokens -= 1
                return True
            return False
    
    def debug(self, message: str, *args, **kwargs):
        if self._can_log():
            super().debug(message, *args, **kwargs)
    
    def info(self, message: str, *args, **kwargs):
        if self._can_log():
            super().info(message, *args, **kwargs)
    
    def warning(self, message: str, *args, **kwargs):
        if self._can_log():
            super().warning(message, *args, **kwargs)
    
    def error(self, message: str, *args, **kwargs):
        if self._can_log():
            super().error(message, *args, **kwargs)
    
    def critical(self, message: str, *args, **kwargs):
        if self._can_log():
            super().critical(message, *args, **kwargs)


class LoggerFactory:
    """
    Factory for creating logger instances.
    """
    
    @staticmethod
    def create_logger(name: str, level: int = logging.INFO) -> Logger:
        """
        Create a new logger.
        
        Args:
            name: Logger name
            level: Logging level
            
        Returns:
            Logger instance
        """
        logger = Logger(name)
        logger.logger.setLevel(level)
        return logger
    
    @staticmethod
    def get_root_logger() -> Logger:
        """
        Get the root logger.
        
        Returns:
            Root logger instance
        """
        return Logger("root")


class MonitoringLogger(Logger):
    """
    Logger focused on monitoring and metrics.
    """
    
    METRIC_TYPES = ["counter", "gauge", "timer", "histogram"]
    
    def __init__(self, name: str):
        super().__init__(name)
        self.metrics: Dict[str, Dict[str, Any]] = {}
    
    def record_metric(self, name: str, value: float, metric_type: str = "gauge", tags: Dict[str, str] = None):
        """
        Record a metric.
        
        Args:
            name: Metric name
            value: Metric value
            metric_type: Type of metric
            tags: Additional tags
        """
        if metric_type not in self.METRIC_TYPES:
            self.warning(f"Invalid metric type: {metric_type}")
            return
        
        tags = tags or {}
        
        self.metrics[name] = {
            "value": value,
            "type": metric_type,
            "tags": tags,
            "timestamp": time.time()
        }
        
        self.info(f"Metric recorded: {name} = {value}", extra={
            "metric_name": name,
            "metric_value": value,
            "metric_type": metric_type,
            "metric_tags": tags
        })
    
    def get_metrics(self) -> Dict[str, Dict[str, Any]]:
        """
        Get all recorded metrics.
        
        Returns:
            Dictionary of metrics
        """
        return self.metrics.copy()
    
    def clear_metrics(self):
        """
        Clear all recorded metrics.
        """
        self.metrics.clear()


class AsyncLogger(Logger):
    """
    Async logger that uses a queue to improve performance.
    """
    
    def __init__(self, name: str, queue_size: int =1000):
        super().__init__(name)
        self.queue = queue.Queue(maxsize=queue_size)
        self.worker_thread = threading.Thread(target=self._worker, daemon=True)
        self.worker_thread.start()
    
    def _worker(self):
        """
        Worker thread for processing log messages.
        """
        while True:
            try:
                record = self.queue.get(timeout=1)
                self.logger.handle(record)
                self.queue.task_done()
            except queue.Empty:
                continue
            except Exception as e:
                self._handle_error(e)
    
    def _handle_error(self, error: Exception):
        """
        Handle errors in the logging worker.
        """
        self.error("Async logger error", exc_info=True)
    
    def _log(self, level: int, message: str, *args, **kwargs):
        """
        Override log method to use queue.
        """
        try:
            record = logging.LogRecord(
                name=self.name,
                level=level,
                pathname=__file__,
                lineno=0,
                msg=message,
                args=args,
                exc_info=kwargs.pop("exc_info", None)
            )
            
            self.queue.put(record, block=True, timeout=1)
        except queue.Full:
            # If queue is full, fallback to synchronous logging
            super()._log(level, message, *args, **kwargs)
    
    def exception(self, message: str = "Exception occurred", *args, **kwargs):
        """
        Log an exception asynchronously.
        """
        try:
            raise kwargs.pop("exc_info")[1]
        except Exception:
            super().exception(message, *args, **kwargs)


class LoggerContextManager:
    """
    Context manager for logging operations.
    """
    
    def __init__(self, logger: Logger):
        self.logger = logger
        self.original_level = self.logger.logger.level
    
    def __enter__(self):
        return self.logger
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.logger.logger.setLevel(self.original_level)


class DebugLogger(Logger):
    """
    Debug logger with detailed output.
    """
    
    def __init__(self, name: str):
        super().__init__(name)
        self.logger.setLevel(logging.DEBUG)
        
        # Add detailed formatter
        self.formatter = logging.Formatter(
            '[%(asctime)s] [%(levelname)s] [%(name)s:%(lineno)d] '
            '[%(module)s.py] %(message)s',
            datefmt='%Y-%m-%d %H:%M:%S.%f'
        )
        
        # Ensure handlers are using the new formatter
        for handler in self.logger.handlers:
            handler.setFormatter(self.formatter)


class QuietLogger(Logger):
    """
    Quiet logger that only logs errors and above.
    """
    
    def __init__(self, name: str):
        super().__init__(name)
        self.logger.setLevel(logging.ERROR)


class LoggingConfiguration:
    """
    Central logging configuration manager.
    """
    
    DEFAULT_CONFIG = {
        "level": "info",
        "handlers": [
            {"type": "console", "level": "info"},
            {"type": "file", "level": "debug", "path": "logs/app.log"}
        ],
        "format": "text"
    }
    
    @classmethod
    def configure_from_dict(cls, config: Dict[str, Any]):
        """
        Configure logging from a dictionary.
        """
        level = config.get("level", "info").lower()
        handlers_config = config.get("handlers", [])
        
        # Set global logging level
        logging.basicConfig(level=cls.LEVELS.get(level, logging.INFO))
        
        # Configure handlers
        for handler_config in handlers_config:
            handler_type = handler_config.get("type")
            if handler_type == "console":
                cls._configure_console_handler(handler_config)
            elif handler_type == "file":
                cls._configure_file_handler(handler_config)
    
    @classmethod
    def _configure_console_handler(cls, config: Dict[str, Any]):
        """
        Configure console handler.
        """
        level = config.get("level", "info").lower()
        formatter = logging.Formatter(
            '[%(asctime)s] [%(levelname)s] [%(name)s] %(message)s',
            datefmt='%Y-%m-%d %H:%M:%S'
        )
        
        handler = logging.StreamHandler()
        handler.setLevel(cls.LEVELS.get(level, logging.INFO))
        handler.setFormatter(formatter)
        logging.getLogger().addHandler(handler)
    
    @classmethod
    def _configure_file_handler(cls, config: Dict[str, Any]):
        """
        Configure file handler.
        """
        level = config.get("level", "debug").lower()
        path = config.get("path", "logs/app.log")
        max_bytes = config.get("max_bytes", 10 * 1024 * 1024)
        backup_count = config.get("backup_count", 5)
        
        handler = FileRotationHandler(
            filename=path,
            max_bytes=max_bytes,
            backup_count=backup_count
        )
        handler.setLevel(cls.LEVELS.get(level, logging.DEBUG))
        handler.setFormatter(logging.Formatter(
            '[%(asctime)s] [%(levelname)s] [%(name)s] %(message)s',
            datefmt='%Y-%m-%d %H:%M:%S'
        ))
        logging.getLogger().addHandler(handler)


class MetricLogger(MonitoringLogger):
    """
    Metric-focused logger for tracking performance and system metrics.
    """
    
    def __init__(self, name: str):
        super().__init__(name)
        self.registered_metrics: Dict[str, Dict[str, Any]] = {}
    
    def register_metric(self, name: str, description: str = "", metric_type: str = "gauge"):
        """
        Register a metric with a description.
        """
        if name in self.registered_metrics:
            return
        
        self.registered_metrics[name] = {
            "description": description,
            "type": metric_type,
            "last_value": None,
            "last_timestamp": None
        }
    
    def get_registered_metrics(self) -> Dict[str, Dict[str, Any]]:
        """
        Get all registered metrics metadata.
        """
        return self.registered_metrics.copy()
    
    def increment(self, name: str, value: float = 1.0, tags: Dict[str, str] = None):
        """
        Increment a counter metric.
        """
        self.record_metric(name, value, "counter", tags)
    
    def set(self, name: str, value: float, tags: Dict[str, str] = None):
        """
        Set a gauge metric.
        """
        self.record_metric(name, value, "gauge", tags)
    
    def timing(self, name: str, duration: float, tags: Dict[str, str] = None):
        """
        Record a timer metric.
        """
        self.record_metric(name, duration, "timer", tags)
    
    def histogram(self, name: str, value: float, tags: Dict[str, str] = None):
        """
        Record a histogram metric.
        """
        self.record_metric(name, value, "histogram", tags)


class StatsDLogger(MetricLogger):
    """
    Logger for StatsD-compatible metric systems.
    """
    
    def __init__(self, name: str, statsd_host: str = "localhost", statsd_port: int = 8125):
        super().__init__(name)
        self.statsd_host = statsd_host
        self.statsd_port = statsd_port
        self._connection = None
    
    def _connect(self):
        """
        Connect to StatsD server.
        """
        if self._connection:
            return True
        
        try:
            import socket
            self._connection = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
            self._connection.settimeout(1)
            return True
        except Exception as e:
            self.error(f"Failed to connect to StatsD: {str(e)}")
            return False
    
    def _send_metric(self, name: str, value: float, metric_type: str, tags: Dict[str, str] = None):
        """
        Send a metric to StatsD.
        """
        if not self._connect():
            return False
        
        if tags:
            tag_string = "".join([f"{k}:{v}," for k, v in tags.items()])[:-1]
            name = f"{name}:{tag_string}"
        
        if metric_type == "counter":
            message = f"{name}:{value}|c"
        elif metric_type == "gauge":
            message = f"{name}:{value}|g"
        elif metric_type == "timer":
            message = f"{name}:{value}|ms"
        elif metric_type == "histogram":
            message = f"{name}:{value}|h"
        else:
            return False
        
        try:
            self._connection.sendto(message.encode(), (self.statsd_host, self.statsd_port))
            return True
        except Exception as e:
            self.error(f"Failed to send metric to StatsD: {str(e)}")
            return False
    
    def increment(self, name: str, value: float = 1.0, tags: Dict[str, str] = None):
        super().increment(name, value, tags)
        self._send_metric(name, value, "counter", tags)
    
    def set(self, name: str, value: float, tags: Dict[str, str] = None):
        super().set(name, value, tags)
        self._send_metric(name, value, "gauge", tags)
    
    def timing(self, name: str, duration: float, tags: Dict[str, str] = None):
        super().timing(name, duration, tags)
        self._send_metric(name, duration, "timer", tags)


class PrometheusLogger(MetricLogger):
    """
    Logger for exposing metrics in Prometheus format.
    """
    
    def __init__(self, name: str, endpoint: str = "/metrics"):
        super().__init__(name)
        self.metrics_registry = {}
        self.endpoint = endpoint
        self._server = None
    
    def _generate_metrics(self) -> str:
        """
        Generate Prometheus-formatted metrics.
        """
        metrics = []
        
        for name, data in self.metrics.items():
            metric_type = data.get("type", "gauge")
            value = data.get("value")
            tags = data.get("tags", {})
            
            if tags:
                label_string = "".join([f"{k}=\"{v}\", " for k, v in tags.items()])[:-2]
                metrics.append(f"{name}{{{label_string}}} {value}")
            else:
                metrics.append(f"{name} {value}")
        
        return "\n".join(metrics)
    
    def start_metrics_server(self, port: int = 9100):
        """
        Start a metrics server.
        """
        from http.server import BaseHTTPRequestHandler, HTTPServer
        
        class MetricsHandler(BaseHTTPRequestHandler):
            def do_GET(self):
                if self.path == self.parent.endpoint:
                    self.send_response(200)
                    self.send_header("Content-Type", "text/plain; version=0.0.4")
                    self.end_headers()
                    self.wfile.write(self.parent._generate_metrics().encode())
            
            def log_message(self, format, *args):
                return  # Suppress default logging
    
        self._server = HTTPServer(("localhost", port), lambda *a, **k: MetricsHandler(*a, parent=self, **k))
        self._server.serve_forever()
    
    def get_metrics_endpoint(self) -> str:
        """
        Get the metrics endpoint URL.
        """
        return f"http://localhost:9100{self.endpoint}"


class LoggingService:
    """
    Central logging service for the application.
    Manages all logging operations and configurations.
    """
    
    def __init__(self):
        self.loggers: Dict[str, Logger] = {}
        self.default_logger = LoggerFactory.create_logger("app")
        self.monitoring_logger = LoggerFactory.create_logger("monitoring", logging.INFO)
        self.metrics_logger = MetricLogger("metrics")
        
        self.configuration = LoggingConfiguration.DEFAULT_CONFIG
    
    def get_logger(self, name: str) -> Logger:
        """
        Get a logger by name.
        """
        if name not in self.loggers:
            self.loggers[name] = LoggerFactory.create_logger(name)
        
        return self.loggers[name]
    
    def configure(self, config: Dict[str, Any]):
        """
        Configure logging service.
        """
        self.configuration = config
        LoggingConfiguration.configure_from_dict(config)
    
    def log(self, level: str, message: str, logger_name: str = "app", **kwargs):
        """
        Log a message with specified level.
        """
        logger = self.get_logger(logger_name)
        log_level = Logger.LEVELS.get(level.lower(), logging.INFO)
        logger.log(log_level, message, **kwargs)
    
    def debug(self, message: str, logger_name: str = "app", **kwargs):
        self.log("debug", message, logger_name, **kwargs)
    
    def info(self, message: str, logger_name: str = "app", **kwargs):
        self.log("info", message, logger_name, **kwargs)
    
    def warning(self, message: str, logger_name: str = "app", **kwargs):
        self.log("warning", message, logger_name, **kwargs)
    
    def error(self, message: str, logger_name: str = "app", **kwargs):
        self.log("error", message, logger_name, **kwargs)
    
    def critical(self, message: str, logger_name: str = "app", **kwargs):
        self.log("critical", message, logger_name, **kwargs)
    
    def exception(self, message: str = "Exception", logger_name: str = "app", **kwargs):
        self.log("error", message, logger_name, **kwargs)
        self.default_logger.exception(message, **kwargs)


class LoggingContext:
    """
    Context manager for logging operations.
    """
    
    def __init__(self, logging_service: LoggingService):
        self.logging_service = logging_service
        self.context_data: Dict[str, Any] = {}
    
    def __enter__(self):
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type:
            self.logging_service.error(
                "Exception occurred in context",
                extra={
                    "exception_type": exc_type.__name__,
                    "exception_message": str(exc_val),
                    "stack_trace": traceback.format_exc()
                }
            )
    
    def info(self, message: str, **kwargs):
        self.logging_service.info(message, **kwargs)
    
    def debug(self, message: str, **kwargs):
        self.logging_service.debug(message, **kwargs)


class AsyncLoggingService(LoggingService):
    """
    Async logging service for high-performance applications.
    """
    
    def __init__(self):
        super().__init__()
        self.queue = queue.Queue(maxsize=1000)
        self.worker_thread = threading.Thread(target=self._worker, daemon=True)
        self.worker_thread.start()
    
    def _worker(self):
        """
        Worker thread for processing log messages.
        """
        while True:
            try:
                record = self.queue.get(timeout=1)
                self._process_record(record)
                self.queue.task_done()
            except queue.Empty:
                continue
            except Exception as e:
                self._handle_error(e)
    
    def _process_record(self, record: Dict[str, Any]):
        """
        Process a log record.
        """
        level = record.get("level")
        message = record.get("message")
        logger_name = record.get("logger_name")
        kwargs = record.get("kwargs", {})
        
        if logger_name in self.loggers:
            logger = self.loggers[logger_name]
            log_level = Logger.LEVELS.get(level.lower(), logging.INFO)
            logger.log(log_level, message, **kwargs)
        else:
            self.default_logger.log(log_level, message, **kwargs)
    
    def _handle_error(self, error: Exception):
        """
        Handle errors in logging worker.
        """
        self.default_logger.error("Async logging error", exc_info=True)
    
    def log(self, level: str, message: str, logger_name: str = "app", **kwargs):
        """
        Async version of log.
        """
        try:
            record = {
                "level": level,
                "message": message,
                "logger_name": logger_name,
                "kwargs": kwargs
            }
            self.queue.put(record, block=True, timeout=1)
        except queue.Full:
            super().log(level, message, logger_name, **kwargs)
    
    def debug(self, message: str, logger_name: str = "app", **kwargs):
        self.log("debug", message, logger_name, **kwargs)
    
    def info(self, message: str, logger_name: str = "app", **kwargs):
        self.log("info", message, logger_name, **kwargs)
    
    def warning(self, message: str, logger_name: str = "app", **kwargs):
        self.log("warning", message, logger_name, **kwargs)
    
    def error(self, message: str, logger_name: str = "app", **kwargs):
        self.log("error", message, logger_name, **kwargs)
    
    def critical(self, message: str, logger_name: str = "app", **kwargs):
        self.log("critical", message, logger_name, **kwargs)


class LoggingPlugin(Plugin):
    """
    Plugin for logging-related operations.
    """
    
    def on_log_message(self, level: str, message: str, **kwargs):
        """
        Handle a log message.
        """
        pass
    
    def on_log_exception(self, message: str, exc_info: tuple):
        """
        Handle an exception log.
        """
        pass
    
    def on_log_metric(self, name: str, value: float, metric_type: str, tags: Dict[str, str]):
        """
        Handle a metric log.
        """
        pass


class AdvancedLoggingPlugin(LoggingPlugin):
    """
    Plugin with advanced logging features.
    """
    
    def on_log_message(self, level: str, message: str, **kwargs):
        # Add contextual information if available
        context = kwargs.pop("context", {})
        if context:
            message = f"[Context: {context}] {message}"
        
        # Add plugin-specific processing
        self._enhance_log_message(level, message, **kwargs)
    
    def _enhance_log_message(self, level: str, message: str, **kwargs):
        """
        Enhance log message with additional processing.
        """
        # Add timestamp if not present
        if "timestamp" not in kwargs:
            kwargs["timestamp"] = time.time()
        
        # Add PID if not present
        if "pid" not in kwargs:
            kwargs["pid"] = os.getpid()
        
        # Add thread ID if not present
        if "thread_id" not in kwargs:
            kwargs["thread_id"] = threading.get_ident()
        
        # Add level if not present
        if "level" not in kwargs:
            kwargs["level"] = level
    
    def on_log_exception(self, level: str, message: str, exc_info: tuple):
        """
        Handle exception logging with additional context.
        """
        exc_type, exc_value, exc_traceback = exc_info
        context = kwargs.pop("context", {})
        
        self._log_exception_with_context(exc_type, exc_value, exc_traceback, context)


class ContextualLogger(Logger):
    """
    Logger that captures and logs contextual information.
    """
    
    def __init__(self, name: str):
        super().__init__(name)
        self.context: Dict[str, Any] = {}
    
    def set_context(self, context: Dict[str, Any]):
        """
        Set contextual information for logs.
        """
        self.context.update(context)
    
    def clear_context(self):
        """
        Clear all contextual information.
        """
        self.context.clear()
    
    def _log(self, level: int, message: str, *args, **kwargs):
        """
        Override log method to include context.
        """
        full_kwargs = self.context.copy()
        full_kwargs.update(kwargs)
        
        super()._log(level, message, *args, **full_kwargs)


class RequestLogger(ContextualLogger):
    """
    Logger for tracking HTTP requests.
    """
    
    REQUEST_FIELDS = {
        "id": "request_id",
        "method": "method",
        "path": "path",
        "status": "status_code",
        "duration": "duration_ms",
        "size": "response_size"
    }
    
    def log_request(self, request, response, duration_ms: float):
        """
        Log an HTTP request.
        """
        context = {
            "request_id": request.id,
            "method": request.method,
            "path": request.path,
            "status_code": response.status_code,
            "duration_ms": duration_ms,
            "response_size": len(response.content)
        }
        
        self.set_context(context)
        self.info("Request processed")


class UserActionLogger(ContextualLogger):
    """
    Logger for tracking user actions.
    """
    
    def log_action(self, user_id: str, action: str, object_id: str = "", object_type: str = ""):
        """
        Log a user action.
        """
        context = {
            "user_id": user_id,
            "action": action,
            "object_id": object_id,
            "object_type": object_type
        }
        
        self.set_context(context)
        self.info("User action recorded")


class SystemLogger(ContextualLogger):
    """
    Logger for system-level events.
    """
    
    def log_startup(self):
        """Log application startup."""
        self.info("Application starting")
    
    def log_shutdown(self):
        """Log application shutdown."""
        self.info("Application shutting down")


class ErrorLogger(ContextualLogger):
    """
    Logger focused on error tracking and reporting.
    """
    
    ERROR_TYPES = {
        "http": "HTTP error",
        "database": "Database error",
        "validation": "Validation error",
        "permission": "Permission error",
        "not_found": "Resource not found",
        "internal": "Internal error"
    }
    
    def log_error(self, error_type: str, message: str, exc_info: tuple = None):
        """
        Log an error with specific type.
        """
        context = {
            "error_type": error_type,
            "message": message
        }
        
        self.set_context(context)
        self.error(message, exc_info=exc_info)


class AuditLogger(ContextualLogger):
    """
    Logger for auditing sensitive operations.
    """
    
    def log_audit(self, user_id: str, action: str, resource: str, details: Dict[str, Any]):
        """
        Log an audit event.
        """
        context = {
            "user_id": user_id,
            "action": action,
            "resource": resource,
            "details": details
        }
        
        self.set_context(context)
        self.info("Audit event recorded")


class LoggingRegistry:
    """
    Registry for managing different logger types.
    """
    
    _registry: Dict[str, type] = {
        "default": Logger,
        "structured": StructuredLogger,
        "monitoring": MonitoringLogger,
        "metric": MetricLogger,
        "statsd": StatsDLogger,
        "prometheus": PrometheusLogger,
        "file": FileRotationHandler,
        "console": Logger,
        "debug": DebugLogger,
        "quiet": QuietLogger,
        "async": AsyncLogger,
        "contextual": ContextualLogger,
        "request": RequestLogger,
        "user_action": UserActionLogger,
        "system": SystemLogger,
        "error": ErrorLogger,
        "audit": AuditLogger
    }
    
    @classmethod
    def get_logger_type(cls, logger_type: str) -> type:
        """
        Get logger type class.
        """
        return cls._registry.get(logger_type, Logger)
    
    @classmethod
    def register_logger_type(cls, name: str, logger_class: type):
        """
        Register a new logger type.
        """
        cls._registry[name] = logger_class


class LoggerFactory:
    """
    Enhanced factory for creating loggers with different types.
    """
    
    @staticmethod
    def create_logger(name: str, logger_type: str = "default", **kwargs) -> Logger:
        """
        Create a logger of specified type.
        """
        logger_class = LoggingRegistry.get_logger_type(logger_type)
        return logger_class(name, **kwargs)
    
    @staticmethod
    def create_configurable_logger(name: str, config: Dict[str, Any]) -> Logger:
        """
        Create a logger based on configuration.
        """
        logger_type = config.get("type", "default")
        level = config.get("level", "info")
        handlers = config.get("handlers", [])
        
        logger = LoggerFactory.create_logger(name, logger_type)
        logger.logger.setLevel(Logger.LEVELS.get(level.lower(), logging.INFO))
        
        for handler_config in handlers:
            handler_type = handler_config.get("type", "console")
            handler = LoggerFactory.create_logger(
                f"{name}-{handler_type}",
                handler_type,
                **handler_config
            )
            logger.add_handler(handler)
        
        return logger
```

5.4 Metrics and Performance Monitoring

```python
# theme_builder/metrics.py

from __future__ import annotations
import time
import threading
import statistics
from collections import defaultdict, deque
from typing import Any, Dict, List, Optional, Tuple, Callable
from dataclasses import dataclass, field
from abc import ABC, abstractmethod
import uuid


@dataclass
class MetricSample:
    """Single metric sample with timestamp."""
    value: float
    timestamp: float = field(default_factory=time.time)


@dataclass
class MetricWindow:
    """Sliding window for metric calculations."""
    samples: deque[MetricSample] = field(default_factory=deque)
    max_size: int = 60  # Default window size in seconds
    
    def add(self, value: float):
        """Add a new metric sample."""
        self.samples.append(MetricSample(value))
        self._prune_old_samples()
    
    def _prune_old_samples(self):
        """Remove samples outside the window."""
        now = time.time()
        cutoff = now - self.max_size
        while self.samples and self.samples[0].timestamp < cutoff:
            self.samples.popleft()
    
    def count(self) -> int:
        """Get number of samples in window."""
        return len(self.samples)
    
    def sum(self) -> float:
        """Get sum of values."""
        return sum(s.value for s in self.samples)
    
    def average(self) -> float:
        """Get average value."""
        if not self.samples:
            return 0.0
        return self.sum() / len(self.samples)
    
    def rate(self) -> float:
        """Get events per second rate."""
        if not self.samples or len(self.samples) < 2:
            return 0.0
        first = self.samples[0].timestamp
        last = self.samples[-1].timestamp
        if last == first:
            return 0.0
        return len(self.samples) / (last - first)


class MetricCalculator:
    """Handles metric calculations and aggregations."""
    
    @staticmethod
    def calculate_percentile(values: List[float], percentile: float = 95.0) -> float:
        """Calculate specified percentile."""
        if not values:
            return 0.0
        sorted_values = sorted(values)
        index = int(len(sorted_values) * percentile / 100)
        return sorted_values[index]
    
    @staticmethod
    def calculate_stddev(values: List[float]) -> float:
        """Calculate standard deviation."""
        if not values:
            return 0.0
        mean = sum(values) / len(values)
        variance = sum((x - mean) ** 2 for x in values) / len(values)
        return variance ** 0.5
    
    @staticmethod
    def calculate_error_rate(errors: int, total: int) -> float:
        """Calculate error rate as percentage."""
        if total == 0:
            return 0.0
        return (errors / total) * 100


class MetricType(ABC):
    """Base class for metric types."""
    
    @abstractmethod
    def record(self, value: float, timestamp: float = None):
        pass
    
    @abstractmethod
    def get(self) -> Dict[str, float]:
        pass


class Gauge(MetricType):
    """Gauge metric - current value."""
    
    def __init__(self, name: str, description: str = ""):
        self.name = name
        self.description = description
        self.value = 0.0
        self.timestamp = time.time()
    
    def record(self, value: float, timestamp: float = None):
        self.value = value
        self.timestamp = timestamp or time.time()
    
    def get(self) -> Dict[str, float]:
        return {
            "value": self.value,
            "timestamp": self.timestamp
        }


class Counter(MetricType):
    """Counter metric - cumulative value."""
    
    def __init__(self, name: str, description: str = ""):
        self.name = name
        self.description = description
        self.value = 0.0
        self._lock = threading.Lock()
    
    def record(self, value: float = 1.0, timestamp: float = None):
        with self._lock:
            self.value += value
    
    def get(self) -> Dict[str, float]:
        return {
            "value": self.value,
            "timestamp": time.time()
        }


class Histogram(MetricType):
    """Histogram metric - distribution tracking."""
    
    def __init__(self, name: str, description: str = ""):
        self.name = name
        self.description = description
        self.samples = []
        self._lock = threading.Lock()
        self.max_samples = 10000
    
    def record(self, value: float, timestamp: float = None):
        with self._lock:
            self.samples.append(value)
            if len(self.samples) > self.max_samples:
                self.samples = self.samples[-self.max_samples:]
    
    def get(self) -> Dict[str, Any]:
        if not self.samples:
            return {
                "count": 0,
                "sum": 0.0,
                "average": 0.0,
                "min": 0.0,
                "max": 0.0,
                "timestamp": time.time()
            }
        
        count = len(self.samples)
        sum_val = sum(self.samples)
        average = sum_val / count
        min_val = min(self.samples)
        max_val = max(self.samples)
        
        return {
            "count": count,
            "sum": sum_val,
            "average": average,
            "min": min_val,
            "max": max_val,
            "timestamp": time.time()
        }


class Timer(MetricType):
    """Timer metric - measures durations."""
    
    def __init__(self, name: str, description: str = ""):
        self.name = name
        self.description = description
        self._histogram = Histogram(f"{name}_distribution", description)
        self._counter = Counter(f"{name}_count", description)
        self._sum = Counter(f"{name}_sum", description)
    
    def record(self, duration: float, timestamp: float = None):
        self._histogram.record(duration, timestamp)
        self._counter.record(1, timestamp)
        self._sum.record(duration, timestamp)
    
    def get(self) -> Dict[str, Any]:
        histogram_data = self._histogram.get()
        return {
            "count": histogram_data["count"],
            "sum": histogram_data["sum"],
            "average": histogram_data["average"],
            "min": histogram_data["min"],
            "max": histogram_data["max"],
            "timestamp": time.time()
        }


class MetricRegistry:
    """Registry for managing metrics."""
    
    def __init__(self):
        self.metrics: Dict[str, MetricType] = {}
        self.lock = threading.Lock()
        self._last_flush = time.time()
        self._flush_interval = 60.0
    
    def register(self, name: str, metric: MetricType):
        """Register a new metric."""
        with self.lock:
            if name not in self.metrics:
                self.metrics[name] = metric
    
    def get(self, name: str) -> Optional[MetricType]:
        """Get a metric by name."""
        with self.lock:
            return self.metrics.get(name)
    
    def record(self, name: str, value: float, timestamp: float = None):
        """Record a metric value."""
        metric = self.get(name)
        if metric:
            metric.record(value, timestamp)
    
    def get_all(self) -> Dict[str, Dict[str, float]]:
        """Get all metrics as dictionary."""
        with self.lock:
            return {name: metric.get() for name, metric in self.metrics.items()}
    
    def flush(self):
        """Flush metrics - can be overridden for reporting."""
        pass


class StatsDMetricReporter:
    """Reporter for StatsD metrics."""
    
    def __init__(
        self,
        host: str = "localhost",
        port: int = 8125,
        prefix: str = "",
        flush_interval: float = 10.0
    ):
        self.host = host
        self.port = port
        self.prefix = prefix.rstrip(".") + "." if prefix else ""
        self.flush_interval = flush_interval
        self.metrics = {}
        self._lock = threading.Lock()
        self._thread = threading.Thread(target=self._run, daemon=True)
        self._thread.start()
    
    def _run(self):
        """Run the reporting loop."""
        while True:
            time.sleep(self.flush_interval)
            self._flush()
    
    def _flush(self):
        """Flush accumulated metrics."""
        with self._lock:
            for name, value in list(self.metrics.items()):
                self._send_metric(name, value)
                del self.metrics[name]
    
    def _send_metric(self, name: str, value: float):
        """Send a single metric to StatsD."""
        import socket
        try:
            full_name = f"{self.prefix}{name}"
            message = f"{full_name}:{value}|c"
            sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
            sock.sendto(message.encode(), (self.host, self.port))
        except Exception as e:
            print(f"Failed to send metric {name}: {e}")


class PrometheusMetricExporter:
    """Exposes metrics in Prometheus format."""
    
    def __init__(self, registry: MetricRegistry, endpoint: str = "/metrics"):
        self.registry = registry
        self.endpoint = endpoint
        self.metrics_cache = {}
        self.lock = threading.Lock()
    
    def generate_metrics(self) -> str:
        """Generate Prometheus-formatted metrics."""
        with self.lock:
            metrics = []
            for name, metric in self.registry.get_all().items():
                # Convert CamelCase to snake_case
                metric_name = name.replace(":", "_").replace(" ", "_").lower()
                
                if "value" in metric:
                    metrics.append(f"{metric_name} {metric['value']}")
                
                # Add additional stats if present
                if "count" in metric:
                    metrics.append(f"{metric_name}_count {metric['count']}")
                if "sum" in metric:
                    metrics.append(f"{metric_name}_sum {metric['sum']}")
                if "average" in metric:
                    metrics.append(f"{metric_name}_avg {metric['average']}")
            
            return "\n".join(metrics)
    
    def start_server(self, port: int = 9100):
        """Start HTTP server for metrics."""
        from http.server import BaseHTTPRequestHandler, HTTPServer
        
        class MetricsHandler(BaseHTTPRequestHandler):
            def do_GET(self):
                if self.path == self.parent.endpoint:
                    self.send_response(200)
                    self.send_header("Content-Type", "text/plain; version=0.0.4")
                    self.end_headers()
                    self.wfile.write(self.parent.generate_metrics().encode())
            
            def log_message(self, format, *args):
                return  # Suppress default logging
        
        handler_class = lambda *a, **k: MetricsHandler(*a, parent=self, **k)
        self.server = HTTPServer(("localhost", port), handler_class)
        self.server.serve_forever()


class MetricLogger:
    """Central metric logging class."""
    
    def __init__(self, registry: Optional[MetricRegistry] = None):
        self.registry = registry or MetricRegistry()
        self.statsd_reporter = None
        self.prometheus_exporter = None
    
    def set_statsd_reporter(self, host: str = "localhost", port: int = 8125):
        """Configure StatsD reporter."""
        self.statsd_reporter = StatsDMetricReporter(host, port)
    
    def set_prometheus_exporter(self, port: int = 9100):
        """Configure Prometheus exporter."""
        self.prometheus_exporter = PrometheusMetricExporter(self.registry)
        self.prometheus_exporter.start_server(port)
    
    def create_metric(self, name: str, metric_type: str = "gauge", description: str = "") -> MetricType:
        """Create and register a new metric."""
        if metric_type == "gauge":
            metric = Gauge(name, description)
        elif metric_type == "counter":
            metric = Counter(name, description)
        elif metric_type == "histogram":
            metric = Histogram(name, description)
        elif metric_type == "timer":
            metric = Timer(name, description)
        else:
            metric = Gauge(name, description)
        
        self.registry.register(name, metric)
        return metric
    
    def record(self, name: str, value: float):
        """Record a metric value."""
        self.registry.record(name, value)
    
    def get_metric(self, name: str) -> Optional[Dict[str, float]]:
        """Get metric data."""
        return self.registry.get(name)


class PerformanceMonitor:
    """Monitors application performance."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.timers = {}
        self._lock = threading.Lock()
    
    def start_timer(self, timer_name: str) -> float:
        """Start a timer and return start time."""
        start_time = time.time()
        self.timers[timer_name] = start_time
        return start_time
    
    def stop_timer(self, timer_name: str, description: str = "") -> float:
        """Stop a timer and record the duration."""
        with self._lock:
            if timer_name in self.timers:
                start_time = self.timers.pop(timer_name)
                duration = time.time() - start_time
                self.metric_logger.record(timer_name, duration)
                return duration
        return 0.0
    
    def record_request_duration(self, request_id: str, duration: float):
        """Record HTTP request duration."""
        self.metric_logger.record(f"requests.duration.{request_id}", duration)
    
    def record_error(self, error_type: str):
        """Record an error occurrence."""
        self.metric_logger.record(f"errors.{error_type}", 1)


class MemoryMonitor:
    """Monitors memory usage."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.gauge = self.metric_logger.create_metric("memory.usage", "gauge", "Memory usage in bytes")
        self._interval = 60.0
        self._thread = threading.Thread(target=self._monitor, daemon=True)
        self._thread.start()
    
    def _monitor(self):
        """Memory monitoring loop."""
        while True:
            try:
                usage = self._get_memory_usage()
                self.metric_logger.record("memory.usage", usage)
                time.sleep(self._interval)
            except Exception as e:
                print(f"Memory monitoring error: {e}")
                time.sleep(10)
    
    def _get_memory_usage(self) -> float:
        """Get current memory usage."""
        try:
            import psutil
            return psutil.virtual_memory().used
        except Exception:
            try:
                import resource
                rusage = resource.getrusage(resource.RUSAGE_SELF)
                return rusage.ru_maxrss * 1024  # Convert KB to bytes
            except Exception:
                return 0.0


class CPUMonitor:
    """Monitors CPU usage."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.gauge = self.metric_logger.create_metric("cpu.usage", "gauge", "CPU usage percentage")
        self._interval = 1.0
        self._thread = threading.Thread(target=self._monitor, daemon=True)
        self._thread.start()
    
    def _monitor(self):
        """CPU monitoring loop."""
        while True:
            try:
                usage = self._get_cpu_usage()
                self.metric_logger.record("cpu.usage", usage)
                time.sleep(self._interval)
            except Exception as e:
                print(f"CPU monitoring error: {e}")
                time.sleep(10)
    
    def _get_cpu_usage(self) -> float:
        """Get current CPU usage percentage."""
        try:
            import psutil
            return psutil.cpu_percent(interval=0.1)
        except Exception:
            return 0.0


class RequestMonitor:
    """Monitors HTTP requests."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.counter = self.metric_logger.create_metric("requests.total", "counter", "Total requests")
        self.timer = self.metric_logger.create_metric("requests.duration", "timer", "Request duration in seconds")
        self.errors_counter = self.metric_logger.create_metric("requests.errors", "counter", "Error requests")
    
    def track_request(self, duration: float, status_code: int):
        """Track an HTTP request."""
        self.counter.record(1)
        self.timer.record(duration)
        if status_code >= 400:
            self.errors_counter.record(1)


class DatabaseMonitor:
    """Monitors database performance."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.queries_counter = self.metric_logger.create_metric("database.queries", "counter", "Total database queries")
        self.duration_timer = self.metric_logger.create_metric("database.query_duration", "timer", "Query duration in milliseconds")
        self.errors_counter = self.metric_logger.create_metric("database.errors", "counter", "Database errors")
    
    def track_query(self, duration: float):
        """Track a database query."""
        self.queries_counter.record(1)
        self.duration_timer.record(duration)


class CacheMonitor:
    """Monitors cache performance."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.hits_counter = self.metric_logger.create_metric("cache.hits", "counter", "Cache hits")
        self.misses_counter = self.metric_logger.create_metric("cache.misses", "counter", "Cache misses")
        self.sets_counter = self.metric_logger.create_metric("cache.sets", "counter", "Cache sets")


class MetricAggregator:
    """Aggregates metrics from multiple sources."""
    
    def __init__(self):
        self.aggregated_metrics = {}
        self.lock = threading.Lock()
    
    def add_metrics(self, metrics: Dict[str, Dict[str, float]]):
        """Add metrics from another source."""
        with self.lock:
            for name, data in metrics.items():
                if name not in self.aggregated_metrics:
                    self.aggregated_metrics[name] = {
                        "value": 0.0,
                        "count": 0
                    }
                self.aggregated_metrics[name]["value"] += data.get("value", 0.0)
                self.aggregated_metrics[name]["count"] += 1
    
    def get_aggregated(self) -> Dict[str, Dict[str, float]]:
        """Get aggregated metrics as average values."""
        with self.lock:
            result = {}
            for name, data in self.aggregated_metrics.items():
                if data["count"] > 0:
                    result[name] = {
                        "value": data["value"] / data["count"],
                        "count": data["count"]
                    }
            return result


class MetricAlerting:
    """Handles metric alerts and notifications."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.alert_rules = []
        self._lock = threading.Lock()
    
    def add_alert_rule(
        self,
        name: str,
        metric: str,
        threshold: float,
        comparison: str = ">",
        duration: float = 60.0,
        severity: str = "warning"
    ):
        """Add an alert rule."""
        with self._lock:
            self.alert_rules.append({
                "name": name,
                "metric": metric,
                "threshold": threshold,
                "comparison": comparison,
                "duration": duration,
                "severity": severity,
                "triggered": False,
                "trigger_time": 0.0
            })
    
    def check_alerts(self):
        """Check all alert rules."""
        with self._lock:
            for rule in self.alert_rules:
                metric_value = self.metric_logger.get_metric(rule["metric"])
                if metric_value and "value" in metric_value:
                    current_value = metric_value["value"]
                    
                    if self._evaluate_condition(current_value, rule["threshold"], rule["comparison"]):
                        if not rule["triggered"]:
                            rule["triggered"] = True
                            rule["trigger_time"] = time.time()
                            self._trigger_alert(rule, current_value)
                    elif rule["triggered"]:
                        rule["triggered"] = False
                        rule["trigger_time"] = 0.0
    
    def _evaluate_condition(self, value: float, threshold: float, comparison: str) -> bool:
        """Evaluate alert condition."""
        if comparison == ">":
            return value > threshold
        elif comparison == ">=":
            return value >= threshold
        elif comparison == "<":
            return value < threshold
        elif comparison == "<=":
            return value <= threshold
        return False
    
    def _trigger_alert(self, rule: Dict[str, Any], value: float):
        """Trigger an alert."""
        message = (
            f"[{rule['severity'].upper()}] Alert: {rule['name']}\n"
            f"Metric: {rule['metric']}\n"
            f"Threshold: {rule['threshold']}\n"
            f"Current value: {value}\n"
            f"Triggered at: {time.ctime()}"
        )
        self.metric_logger.record("alerts", 1)
        print(message)


class MetricExporter:
    """Exports metrics to external systems."""
    
    def __init__(self, metric_logger: MetricLogger):
        self.metric_logger = metric_logger
        self.exporters = []
    
    def add_exporter(self, exporter_type: str, **kwargs):
        """Add an exporter."""
        if exporter_type == "statsd":
            self.exporters.append(StatsDMetricReporter(**kwargs))
        elif exporter_type == "prometheus":
            self.exporters.append(PrometheusMetricExporter(self.metric_logger.registry, **kwargs))
        # Add more exporter types as needed
    
    def export_all(self):
        """Export all metrics."""
        metrics = self.metric_logger.registry.get_all()
        for exporter in self.exporters:
            exporter.send(metrics)


class PerformanceMetrics:
    """
    Central performance metrics collection and tracking.
    Monitors key application performance indicators across different components.
    """
    
    def __init__(self):
        from collections import defaultdict
        import threading
        
        self.metrics = defaultdict(lambda: defaultdict(float))
        self.counters = defaultdict(int)
        self.timers = defaultdict(list)
        self.lock = threading.Lock()
        self._last_report = time.time()
        self._report_interval = 60.0  # Report every 60 seconds
        
        # Component-specific metrics
        self.http_metrics = {
            "requests_total": 0,
            "requests_duration_seconds": [],
            "errors_total": 0,
            "status_codes": defaultdict(int)
        }
        
        self.database_metrics = {
            "queries_total": 0,
            "query_duration_seconds": [],
            "errors_total": 0
        }
        
        self.cache_metrics = {
            "hits_total": 0,
            "misses_total": 0,
            "sets_total": 0
        }
    
    def track_http_request(self, duration_seconds: float, status_code: int):
        """
        Track an HTTP request and its performance metrics.
        """
        with self.lock:
            self.http_metrics["requests_total"] += 1
            self.http_metrics["requests_duration_seconds"].append(duration_seconds)
            self.http_metrics["status_codes"][status_code] += 1
            
            if status_code >= 400:
                self.http_metrics["errors_total"] += 1
    
    def track_database_query(self, duration_seconds: float):
        """
        Track a database query and its performance.
        """
        with self.lock:
            self.database_metrics["queries_total"] += 1
            self.database_metrics["query_duration_seconds"].append(duration_seconds)
    
    def track_cache_operation(self, operation: str):
        """
        Track cache operations (get, set, delete).
        """
        with self.lock:
            if operation == "get":
                self.cache_metrics["hits_total"] += 1
            elif operation == "set":
                self.cache_metrics["sets_total"] += 1
            elif operation == "miss":
                self.cache_metrics["misses_total"] += 1
    
    def get_http_stats(self) -> Dict[str, Any]:
        """
        Calculate and return HTTP performance statistics.
        """
        with self.lock:
            if not self.http_metrics["requests_total"]:
                return {
                    "requests_total": 0,
                    "average_duration": 0.0,
                    "p50_duration": 0.0,
                    "p95_duration": 0.0,
                    "errors_rate": 0.0,
                    "status_distribution": {}
                }
            
            durations = self.http_metrics["requests_duration_seconds"]
            requests = self.http_metrics["requests_total"]
            errors = self.http_metrics["errors_total"]
            
            average_duration = sum(durations) / len(durations)
            p50_duration = sorted(durations)[len(durations) // 2]
            p95_duration = sorted(durations)[int(len(durations) * 0.95)]
            
            errors_rate = (errors / requests) * 100
            status_distribution = dict(self.http_metrics["status_codes"])
            
            return {
                "requests_total": requests,
                "average_duration_seconds": average_duration,
                "p50_duration_seconds": p50_duration,
                "p95_duration_seconds": p95_duration,
                "errors_rate_percent": errors_rate,
                "status_distribution": status_distribution
            }
    
    def get_database_stats(self) -> Dict[str, Any]:
        """
        Calculate and return database performance statistics.
        """
        with self.lock:
            if not self.database_metrics["queries_total"]:
                return {
                    "queries_total": 0,
                    "average_duration_seconds": 0.0,
                    "p50_duration_seconds": 0.0,
                    "p95_duration_seconds": 0.0,
                    "errors_total": 0
                }
            
            durations = self.database_metrics["query_duration_seconds"]
            queries = self.database_metrics["queries_total"]
            errors = self.database_metrics["errors_total"]
            
            average_duration = sum(durations) / len(durations)
            p50_duration = sorted(durations)[len(durations) // 2]
            p95_duration = sorted(durations)[int(len(durations) * 0.95)]
            
            return {
                "queries_total": queries,
                "average_duration_seconds": average_duration,
                "p50_duration_seconds": p50_duration,
                "p95_duration_seconds": p95_duration,
                "errors_total": errors
            }
    
    def get_cache_stats(self) -> Dict[str, Any]:
        """
        Calculate and return cache performance statistics.
        """
        with self.lock:
            total_operations = (
                self.cache_metrics["hits_total"] +
                self.cache_metrics["misses_total"] +
                self.cache_metrics["sets_total"]
            )
            
            if not total_operations:
                return {
                    "hits_total": 0,
                    "misses_total": 0,
                    "sets_total": 0,
                    "hit_rate_percent": 0.0
                }
            
            hit_rate = (self.cache_metrics["hits_total"] / total_operations) * 100
            
            return {
                "hits_total": self.cache_metrics["hits_total"],
                "misses_total": self.cache_metrics["misses_total"],
                "sets_total": self.cache_metrics["sets_total"],
                "hit_rate_percent": hit_rate
            }
    
    def reset_metrics(self):
        """
        Reset all performance metrics to zero.
        """
        with self.lock:
            self.http_metrics = {
                "requests_total": 0,
                "requests_duration_seconds": [],
                "errors_total": 0,
                "status_codes": defaultdict(int)
            }
            
            self.database_metrics = {
                "queries_total": 0,
                "query_duration_seconds": [],
                "errors_total": 0
            }
            
            self.cache_metrics = {
                "hits_total": 0,
                "misses_total": 0,
                "sets_total": 0
            }
    
    def report_metrics(self):
        """
        Generate a comprehensive performance report.
        """
        with self.lock:
            self._last_report = time.time()
            return {
                "timestamp": time.time(),
                "http": self.get_http_stats(),
                "database": self.get_database_stats(),
                "cache": self.get_cache_stats()
            }


class PerformanceMonitor:
    """
    Monitors application performance across different components.
    Provides detailed metrics and analysis capabilities.
    """
    
    def __init__(self):
        self.metrics = PerformanceMetrics()
        self.alerts = []
        self.interval = 60  # Default monitoring interval in seconds
        self.is_running = False
        self.thread = None
    
    def start(self):
        """
        Start the performance monitoring system.
        """
        if not self.is_running:
            self.is_running = True
            self.thread = threading.Thread(target=self._monitor_loop, daemon=True)
            self.thread.start()
            print("Performance monitoring started")
    
    def stop(self):
        """
        Stop the performance monitoring system.
        """
        if self.is_running:
            self.is_running = False
            if self.thread:
                self.thread.join()
            print("Performance monitoring stopped")
    
    def _monitor_loop(self):
        """
        Internal loop for performance monitoring.
        """
        while self.is_running:
            self._collect_metrics()
            time.sleep(self.interval)
    
    def _collect_metrics(self):
        """
        Collect performance metrics from various components.
        """
        # This method would typically interface with actual monitoring tools
        # For demonstration, we'll generate some sample metrics
        import random
        import time
        
        # Simulate HTTP request tracking
        for _ in range(5):
            duration = random.uniform(0.05, 0.5)
            status_code = random.choice([200, 201, 204, 400, 401, 404, 500])
            self.metrics.track_http_request(duration, status_code)
        
        # Simulate database queries
        for _ in range(3):
            duration = random.uniform(0.1, 2.0)
            self.metrics.track_database_query(duration)
        
        # Simulate cache operations
        operations = ["get", "get", "set", "get", "miss", "set"]
        for op in operations:
            self.metrics.track_cache_operation(op)
        
        # Check for performance alerts
        self._check_alerts()
    
    def _check_alerts(self):
        """
        Check for performance alerts based on predefined thresholds.
        """
        http_stats = self.metrics.get_http_stats()
        db_stats = self.metrics.get_database_stats()
        cache_stats = self.metrics.get_cache_stats()
        
        # Check HTTP performance alerts
        if http_stats["average_duration_seconds"] > 0.5:
            self._trigger_alert(
                "High HTTP Response Time",
                f"Average response time ({http_stats['average_duration_seconds']:.2f}s) exceeds 0.5s",
                "warning"
            )
        
        if http_stats["errors_rate_percent"] > 5.0:
            self._trigger_alert(
                "High HTTP Error Rate",
                f"Error rate ({http_stats['errors_rate_percent']:.2f}%) exceeds 5%",
                "critical"
            )
        
        # Check database performance alerts
        if db_stats["average_duration_seconds"] > 1.0:
            self._trigger_alert(
                "High Database Query Time",
                f"Average query time ({db_stats['average_duration_seconds']:.2f}s) exceeds 1s",
                "warning"
            )
        
        # Check cache performance alerts
        if cache_stats["hit_rate_percent"] < 80.0:
            self._trigger_alert(
                "Low Cache Hit Rate",
                f"Cache hit rate ({cache_stats['hit_rate_percent']:.2f}%) below 80%",
                "warning"
            )
    
    def _trigger_alert(self, alert_name: str, message: str, severity: str):
        """
        Trigger a performance alert.
        """
        alert_id = str(uuid.uuid4())
        timestamp = time.time()
        
        alert = {
            "id": alert_id,
            "name": alert_name,
            "message": message,
            "severity": severity,
            "timestamp": timestamp,
            "resolved": False
        }
        
        self.alerts.append(alert)
        print(f"[{severity.upper()}] {alert_name}: {message}")
        return alert
    
    def resolve_alert(self, alert_id: str):
        """
        Resolve a previously triggered alert.
        """
        for alert in self.alerts:
            if alert["id"] == alert_id:
                alert["resolved"] = True
                alert["resolution_time"] = time.time()
                print(f"[RESOLVED] {alert['name']}")
                return True
        return False
    
    def get_active_alerts(self) -> List[Dict[str, Any]]:
        """
        Get all active (unresolved) alerts.
        """
        return [alert for alert in self.alerts if not alert.get("resolved")]
    
    def generate_report(self) -> Dict[str, Any]:
        """
        Generate a comprehensive performance report.
        """
        return {
            "timestamp": time.time(),
            "metrics": self.metrics.report_metrics(),
            "alerts": self.get_active_alerts(),
            "summary": self._generate_summary()
        }
    
    def _generate_summary(self) -> Dict[str, str]:
        """
        Generate a textual summary of current performance state.
        """
        metrics = self.metrics.get_http_stats()
        summary = {
            "overall_status": "Everything looks good",
            "key_metrics": []
        }
        
        # HTTP performance
        http_status = "Normal"
        if metrics["average_duration_seconds"] > 0.5:
            http_status = "Warning - Slow responses"
        if metrics["errors_rate_percent"] > 5:
            http_status = "Critical - High errors"
        
        summary["key_metrics"].append({
            "component": "HTTP Server",
            "status": http_status,
            "requests_per_second": round(metrics["requests_total"] / 60, 2),
            "average_response_time": f"{metrics["average_duration_seconds"]:.2f}s",
            "error_rate": f"{metrics["errors_rate_percent"]:.2f}%"
        })
        
        # Database performance
        db_metrics = self.metrics.get_database_stats()
        db_status = "Normal"
        if db_metrics["average_duration_seconds"] > 1.0:
            db_status = "Warning - Slow queries"
        
        summary["key_metrics"].append({
            "component": "Database",
            "status": db_status,
            "queries_per_second": round(db_metrics["queries_total"] / 60, 2),
            "average_query_time": f"{db_metrics["average_duration_seconds"]:.2f}s"
        })
        
        # Cache performance
        cache_metrics = self.metrics.get_cache_stats()
        cache_status = "Normal"
        if cache_metrics["hit_rate_percent"] < 80:
            cache_status = "Warning - Low hits"
        
        summary["key_metrics"].append({
            "component": "Cache",
            "status": cache_status,
            "hit_rate": f"{cache_metrics["hit_rate_percent"]:.2f}%",
            "total_hits": cache_metrics["hits_total"]
        })
        
        return summary


class PerformanceAnalysis:
    """
    Advanced performance analysis tools for root cause identification.
    Provides statistical analysis and diagnostic capabilities.
    """
    
    @staticmethod
    def find_slowest_queries(database_queries: List[Tuple[str, float]]) -> List[Tuple[str, float]]:
        """
        Identify the slowest database queries.
        """
        if not database_queries:
            return []
        
        # Sort queries by duration in descending order
        sorted_queries = sorted(database_queries, key=lambda x: x[1], reverse=True)
        
        # Return top 10 slowest queries
        return sorted_queries[:10]
    
    @staticmethod
    def analyze_request_latency(request_durations: List[float]) -> Dict[str, float]:
        """
        Analyze request latency distribution.
        """
        if not request_durations:
            return {
                "count": 0,
                "total": 0.0,
                "average": 0.0,
                "median": 0.0,
                "p50": 0.0,
                "p95": 0.0,
                "p99": 0.0,
                "min": 0.0,
                "max": 0.0
            }
        
        sorted_durations = sorted(request_durations)
        count = len(sorted_durations)
        total = sum(request_durations)
        average = total / count
        median = sorted_durations[count // 2]
        
        # Calculate percentiles
        p50 = sorted_durations[count // 2]
        p95 = sorted_durations[int(count * 0.95)]
        p99 = sorted_durations[int(count * 0.99)]
        
        min_duration = min(request_durations)
        max_duration = max(request_durations)
        
        return {
            "count": count,
            "total_seconds": total,
            "average_seconds": average,
            "median_seconds": median,
            "p50_seconds": p50,
            "p95_seconds": p95,
            "p99_seconds": p99,
            "min_seconds": min_duration,
            "max_seconds": max_duration
        }
    
    @staticmethod
    def identify_bottlenecks(
        metrics: Dict[str, Any]
    ) -> List[Dict[str, Any]]:
        """
        Identify potential performance bottlenecks.
        """
        bottlenecks = []
        
        # Check HTTP performance
        http_metrics = metrics.get("http", {})
        if http_metrics:
            if http_metrics.get("average_duration_seconds", 0) > 0.5:
                bottlenecks.append({
                    "type": "http",
                    "description": "High average response time",
                    "value": http_metrics.get("average_duration_seconds", 0),
                    "threshold": 0.5,
                    "severity": "high"
                })
            
            if http_metrics.get("errors_rate_percent", 0) > 5:
                bottlenecks.append({
                    "type": "http",
                    "description": "High error rate",
                    "value": http_metrics.get("errors_rate_percent", 0),
                    "threshold": 5,
                    "severity": "critical"
                })
        
        # Check database performance
        db_metrics = metrics.get("database", {})
        if db_metrics:
            if db_metrics.get("average_duration_seconds", 0) > 1.0:
                bottlenecks.append({
                    "type": "database",
                    "description": "High average query time",
                    "value": db_metrics.get("average_duration_seconds", 0),
                    "threshold": 1.0,
                    "severity": "high"
                })
        
        # Check cache performance
        cache_metrics = metrics.get("cache", {})
        if cache_metrics:
            if cache_metrics.get("hit_rate_percent", 0) < 80:
                bottlenecks.append({
                    "type": "cache",
                    "description": "Low cache hit rate",
                    "value": cache_metrics.get("hit_rate_percent", 0),
                    "threshold": 80,
                    "severity": "medium"
                })
        
        # Sort bottlenecks by severity
        return sorted(bottlenecks, key=lambda x: ["critical", "high", "medium"].index(x["severity"]))
    
    @staticmethod
    def generate_performance_recommendations(
        analysis_results: Dict[str, Any]
    ) -> List[str]:
        """
        Generate performance optimization recommendations.
        """
        recommendations = []
        
        # HTTP layer recommendations
        if analysis_results.get("http", {}).get("errors_rate_percent", 0) > 5:
            recommendations.append("Implement comprehensive error logging and monitoring")
            recommendations.append("Review recent code changes for potential bugs")
            recommendations.append("Consider adding circuit breakers")
        
        if analysis_results.get("http", {}).get("average_duration_seconds", 0) > 0.5:
            recommendations.append("Optimize response handling and data processing")
            recommendations.append("Consider caching frequent responses")
            recommendations.append("Review API response sizes")
        
        # Database recommendations
        if analysis_results.get("database", {}).get("average_duration_seconds", 0) > 1.0:
            recommendations.append("Analyze and optimize slow queries")
            recommendations.append("Review database indexing strategy")
            recommendations.append("Consider query caching")
            recommendations.append("Look into database configuration tuning")
        
        # Cache recommendations
        if analysis_results.get("cache", {}).get("hit_rate_percent", 0) < 80:
            recommendations.append("Review caching strategy and cache keys")
            recommendations.append("Consider increasing cache TTL for frequently accessed data")
            recommendations.append("Implement more effective caching layers")
        
        # General recommendations
        recommendations.append("Monitor and analyze periodically")
        recommendations.append("Consider distributed tracing for detailed insights")
        recommendations.append("Perform load testing to identify limits")
        
        return recommendations


class PerformanceDashboard:
    """
    Web-based performance dashboard for real-time monitoring.
    Provides visualizations and interactive metrics exploration.
    """
    
    def __init__(self, performance_monitor: PerformanceMonitor):
        self.monitor = performance_monitor
        self.templates = {
            "index": """
<!DOCTYPE html>
<html>
<head>
    <title>Performance Monitoring Dashboard</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 20px; }
        h1 { color: #333; }
        .container { display: flex; flex-direction: column; gap: 20px; }
        .metrics-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 15px; }
        .metric-card {
            background: #f0f2f5;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .metric-value { font-size: 24px; font-weight: bold; color: #333; }
        .metric-label { font-size: 14px; color: #666; }
        .alert { background: #ffe6e6; padding: 10px; border-left: 5px solid #f44336; }
        .table-container { max-height: 300px; overflow-y: auto; }
        table { width: 100%; border-collapse: collapse; }
        th, td { padding: 8px; border-bottom: 1px solid #ddd; text-align: left; }
        th { background: #f5f5f5; }
    </style>
</head>
<body>
    <h1>Application Performance Monitoring</h1>
    
    <div class="container">
        <div class="metrics-grid">
            <!-- HTTP Metrics -->
            <div class="metric-card">
                <h3>HTTP Server</h3>
                <div class="metric-value">{{ http_requests_total }}</div>
                <div class="metric-label">Total Requests</div>
                <div class="metric-value">{{ http_average_duration }}</div>
                <div class="metric-label">Avg Response Time</div>
                <div class="metric-value">{{ http_errors_rate }}</div>
                <div class="metric-label">Error Rate</div>
            </div>
            
            <!-- Database Metrics -->
            <div class="metric-card">
                <h3>Database</h3>
                <div class="metric-value">{{ db_queries_total }}</div>
                <div class="metric-label">Total Queries</div>
                <div class="metric-value">{{ db_average_duration }}</div>
                <div class="metric-label">Avg Query Time</div>
                <div class="metric-value">{{ db_errors_total }}</div>
                <div class="metric-label">Errors</div>
            </div>
            
            <!-- Cache Metrics -->
            <div class="metric-card">
                <h3>Cache</h3>
                <div class="metric-value">{{ cache_hits }}</div>
                <div class="metric-label">Cache Hits</div>
                <div class="metric-value">{{ cache_misses }}</div>
                <div class="metric-label">Cache Misses</div>
                <div class="metric-value">{{ cache_hit_rate }}</div>
                <div class="metric-label">Hit Rate</div>
            </div>
        </div>
        
        <!-- Alerts Section -->
        <div class="metric-card">
            <h3>Active Alerts</h3>
            {% if alerts %}
                <div class="alert">
                    <strong>{{ alerts[0]['name'] }}</strong>: {{ alerts[0]['message'] }}
                </div>
            {% else %}
                <p>No active alerts.</p>
            {% endif %}
        </div>
        
        <!-- Detailed Metrics -->
        <div class="metric-card">
            <h3>Recent HTTP Requests</h3>
            <table class="table-container">
                <thead>
                    <tr>
                        <th>Timestamp</th>
                        <th>Duration (s)</th>
                        <th>Status Code</th>
                    </tr>
                </thead>
                <tbody>
                    {% for request in recent_requests %}
                        <tr>
                            <td>{{ request['timestamp'] }}</td>
                            <td>{{ request['duration'] }}</td>
                            <td>{{ request['status'] }}</td>
                        </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
</body>
</html>
""",
            "api": """
{
    "/metrics": {
        "GET": "Retrieve performance metrics"
    },
    "/alerts": {
        "GET": "List active alerts"
    },
    "/health": {
        "GET": "Check system health"
    }
}
"""
        }
    
    def start_server(self, host: str = "0.0.0.0", port: int = 8000):
        """
        Start the performance dashboard web server.
        """
        from http.server import BaseHTTPRequestHandler, HTTPServer
        import json
        
        class DashboardHandler(BaseHTTPRequestHandler):
            def do_GET(self):
                if self.path == "/":
                    self.send_response(200)
                    self.send_header("Content-type", "text/html")
                    self.end_headers()
                    self.wfile.write(bytes(self._render_index(), "utf-8"))
                
                elif self.path == "/metrics":
                    self.send_response(200)
                    self.send_header("Content-type", "application/json")
                    self.end_headers()
                    self.wfile.write(bytes(json.dumps(self._get_metrics()), "utf-8"))
                
                elif self.path == "/alerts":
                    self.send_response(200)
                    self.send_header("Content-type", "application/json")
                    self.end_headers()
                    self.wfile.write(bytes(json.dumps(self._get_alerts()), "utf-8"))
                
                elif self.path == "/health":
                    self.send_response(200)
                    self.send_header("Content-type", "application/json")
                    self.end_headers()
                    self.wfile.write(bytes(json.dumps({"status": "healthy"}), "utf-8"))
                
                else:
                    self.send_response(404)
                    self.end_headers()
                    self.wfile.write(b"Not Found")
            
            def _render_index(self):
                metrics = self._get_metrics()
                return self.templates["index"].replace(
                    "{{ http_requests_total }}", str(metrics["http"]["requests_total"])
                ).replace(
                    "{{ http_average_duration }}", f"{metrics["http"]["average_duration_seconds"]:.2f}s"
                ).replace(
                    "{{ http_errors_rate }}", f"{metrics["http"]["errors_rate_percent"]:.2f}%"
                ).replace(
                    "{{ db_queries_total }}", str(metrics["database"]["queries_total"])
                ).replace(
                    "{{ db_average_duration }}", f"{metrics["database"]["average_duration_seconds"]:.2f}s"
                ).replace(
                    "{{ db_errors_total }}", str(metrics["database"]["errors_total"])
                ).replace(
                    "{{ cache_hits }}", str(metrics["cache"]["hits_total"])
                ).replace(
                    "{{ cache_misses }}", str(metrics["cache"]["misses_total"])
                ).replace(
                    "{{ cache_hit_rate }}", f"{metrics["cache"]["hit_rate_percent"]:.2f}%"
                )
            
            def _get_metrics(self):
                return self.server.metrics.generate_report()
            
            def _get_alerts(self):
                return self.server.metrics.get_active_alerts()
        
        class MetricsServer(HTTPServer):
            def __init__(self, metrics_monitor):
                self.metrics = metrics_monitor
                super().__init__((host, port), DashboardHandler)
        
        self.http_server = MetricsServer(self.monitor)
        print(f"Performance dashboard running on http://{host}:{port}")
        self.http_server.serve_forever()
    
    def stop_server(self):
        """Stop the web server."""
        if hasattr(self, "http_server"):
            self.http_server.shutdown()


class MetricCollection:
    """
    Comprehensive metric collection system with multiple reporters.
    Supports various metric types and reporting destinations.
    """
    
    def __init__(self):
        self.registry = MetricRegistry()
        self.reporters = []
        self.gauges = {}
        self.counters = {}
        self.timers = {}
        self.lock = threading.Lock()
    
    def register_gauge(self, name: str, value: float = 0.0, description: str = "") -> Gauge:
        """
        Register a gauge metric.
        """
        with self.lock:
            if name not in self.gauges:
                gauge = Gauge(name, description)
                self.registry.register(name, gauge)
                self.gauges[name] = gauge
            return self.gauges[name]
    
    def register_counter(self, name: str, description: str = "") -> Counter:
        """
        Register a counter metric.
        """
        with self.lock:
            if name not in self.counters:
                counter = Counter(name, description)
                self.registry.register(name, counter)
                self.counters[name] = counter
            return self.counters[name]
    
    def register_timer(self, name: str, description: str = "") -> Timer:
        """
        Register a timer metric.
        """
        with self.lock:
            if name not in self.timers:
                timer = Timer(name, description)
                self.registry.register(name, timer)
                self.timers[name] = timer
            return self.timers[name]
    
    def get_metric(self, name: str) -> Optional[Dict[str, Any]]:
        """
        Retrieve metric data by name.
        """
        with self.lock:
            return self.registry.get(name)
    
    def record_metric(self, name: str, value: float):
        """
        Record a metric value.
        """
        with self.lock:
            self.registry.record(name, value)
    
    def increment_counter(self, name: str, amount: int = 1):
        """
        Increment a counter metric.
        """
        with self.lock:
            if name in self.counters:
                self.counters[name].record(amount)
    
    def update_gauge(self, name: str, value: float):
        """
        Update a gauge metric.
        """
        with self.lock:
            if name in self.gauges:
                self.gauges[name].record(value)
    
    def track_timer(self, name: str, duration: float):
        """
        Track a timer metric.
        """
        with self.lock:
            if name in self.timers:
                self.timers[name].record(duration)
    
    def add_reporter(self, reporter_type: str, **kwargs):
        """
        Add a metrics reporter.
        """
        if reporter_type == "console":
            self.reporters.append(ConsoleReporter())
        elif reporter_type == "statsd":
            host = kwargs.get("host", "localhost")
            port = kwargs.get("port", 8125)
            self.reporters.append(StatsDMetricReporter(host, port))
        elif reporter_type == "prometheus":
            self.reporters.append(PrometheusMetricExporter(self.registry, **kwargs))
        # Add more reporter types as needed
    
    def start_reporting(self, interval: float = 10.0):
        """
        Start periodic metrics reporting.
        """
        self.reporting_interval = interval
        self.reporting_thread = threading.Thread(
            target=self._report_loop,
            daemon=True
        )
        self.reporting_thread.start()
    
    def _report_loop(self):
        """
        Internal loop for metrics reporting.
        """
        while True:
            self._report_metrics()
            time.sleep(self.reporting_interval)
    
    def _report_metrics(self):
        """
        Collect and report metrics to all reporters.
        """
        metrics = self.registry.get_all()
        for reporter in self.reporters:
            reporter.report(metrics)


class MetricReporter:
    """
    Base class for metric reporters.
    """
    
    def report(self, metrics: Dict[str, Dict[str, float]]):
        """
        Report metrics to destination.
        """
        raise NotImplementedError


class ConsoleReporter(MetricReporter):
    """
    Reporter that prints metrics to console.
    """
    
    def report(self, metrics: Dict[str, Dict[str, float]]):
        """
        Print metrics to console in a readable format.
        """
        print("=" * 60)
        print(f"Metrics Report - {time.ctime()}")
        print("=" * 60)
        
        for metric_name, data in metrics.items():
            print(f"\nMetric: {metric_name}")
            for key, value in data.items():
                print(f"  {key}: {value}")
        
        print("\n" + "=" * 60)


class StatsDMetricReporter(MetricReporter):
    """
    Reporter that sends metrics to StatsD server.
    """
    
    def __init__(self, host: str = "localhost", port: int = 8125):
        self.host = host
        self.port = port
        import socket
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    def report(self, metrics: Dict[str, Dict[str, float]]):
        """
        Send metrics to StatsD server.
        """
        for metric_name, data in metrics.items():
            for key, value in data.items():
                if key == "value":
                    self._send_metric(metric_name, value)


class PrometheusMetricExporter(MetricReporter):
    """
    Exposes metrics via HTTP in Prometheus format.
    """
    
    def __init__(self, registry: MetricRegistry, endpoint: str = "/metrics"):
        self.registry = registry
        self.endpoint = endpoint
        self.metrics_cache = {}
        self.lock = threading.Lock()
    
    def start_server(self, host: str = "localhost", port: int = 9100):
        """
        Start HTTP server for metrics exposure.
        """
        from http.server import BaseHTTPRequestHandler, HTTPServer
        
        class MetricsHandler(BaseHTTPRequestHandler):
            def do_GET(self):
                if self.path == self.parent.endpoint:
                    self.send_response(200)
                    self.send_header("Content-Type", "text/plain; version=0.0.4")
                    self.end_headers()
                    self.wfile.write(self.parent.generate_metrics().encode())
            
            def log_message(self, format, *args):
                return  # Suppress default logging
        
        handler_class = lambda *a, **k: MetricsHandler(*a, parent=self, **k)
        self.server = HTTPServer((host, port), handler_class)
        self.server.serve_forever()
    
    def generate_metrics(self) -> str:
        """
        Generate Prometheus-formatted metrics.
        """
        with self.lock:
            metrics = []
            for name, data in self.registry.get_all().items():
                if "value" in data:
                    metrics.append(f"{name} {data["value"]}")
            return "\n".join(metrics)


class MetricAggregator:
    """
    Aggregates metrics from multiple sources.
    """
    
    def __init__(self):
        self.metrics = defaultdict(lambda: defaultdict(float))
        self.counts = defaultdict(int)
        self.lock = threading.Lock()
    
    def add_metrics(self, source: str, metrics: Dict[str, Dict[str, float]]):
        """
        Add metrics from a source.
        """
        with self.lock:
            for metric_name, data in metrics.items():
                key = (source, metric_name)
                self.metrics[key]["value"] += data.get("value", 0.0)
                self.metrics[key]["count"] += 1
    
    def get_aggregated(self) -> Dict[str, Dict[str, float]]:
        """
        Get aggregated metrics as average values.
        """
        with self.lock:
            result = {}
            for (source, metric_name), data in self.metrics.items():
                if data["count"] > 0:
                    average = data["value"] / data["count"]
                    result[f"{source}.{metric_name}"] = {
                        "value": average,
                        "count": data["count"]
                    }
            return result


class MetricAlertManager:
    """
    Manages performance alerts and notifications.
    """
    
    def __init__(self, metric_collection: MetricCollection):
        self.metrics = metric_collection
        self.alert_rules = []
        self.active_alerts = set()
        self.lock = threading.Lock()
    
    def add_alert_rule(
        self,
        name: str,
        metric: str,
        threshold: float,
        comparison: str = ">",
        duration: int = 60,
        severity: str = "warning",
        actions: Optional[List[str]] = None
    ):
        """
        Add an alert rule with specific conditions.
        """
        if actions is None:
            actions = []
        
        rule = {
            "name": name,
            "metric": metric,
            "threshold": threshold,
            "comparison": comparison,
            "duration": duration,
            "severity": severity,
            "actions": actions,
            "triggered_at": 0,
            "resolved": False
        }
        
        self.alert_rules.append(rule)
    
    def evaluate_alerts(self):
        """
        Evaluate all alert rules and trigger alerts when conditions met.
        """
        current_time = time.time()
        metrics_data = self.metrics.registry.get_all()
        
        alerts_to_trigger = []
        alerts_to_resolve = []
        
        # Check each alert rule
        for rule in self.alert_rules:
            metric_key = rule["metric"]
            threshold = rule["threshold"]
            comparison = rule["comparison"]
            duration = rule["duration"]
            
            # Get current metric value
            metric_value = metrics_data.get(metric_key, {}).get("value", 0.0)
            
            # Evaluate condition
            condition_met = self._evaluate_condition(metric_value, threshold, comparison)
            
            # Check if already triggered
            if rule.get("triggered", False):
                # Calculate duration since trigger
                time_since_trigger = current_time - rule["triggered_at"]
                
                if not condition_met:
                    # Condition resolved - add to resolve list
                    alerts_to_resolve.append(rule["name"])
                elif time_since_trigger >= duration:
                    # Duration exceeded - trigger alert
                    alerts_to_trigger.append((rule, current_time))
            
            elif condition_met:
                # Not triggered but condition met - trigger alert
                alerts_to_trigger.append((rule, current_time))
        
        # Trigger new alerts
        for rule, trigger_time in alerts_to_trigger:
            alert_id = f"{rule["name"]}_{int(trigger_time)}"
            self.active_alerts.add(alert_id)
            self._execute_alert_actions(rule, trigger_time)
        
        # Resolve alerts
        for alert_name in alerts_to_resolve:
            self._resolve_alert(alert_name)
    
    def _evaluate_condition(self, value: float, threshold: float, comparison: str) -> bool:
        """
        Evaluate if condition is met.
        """
        if comparison == ">":
            return value > threshold
        elif comparison == ">=":
            return value >= threshold
        elif comparison == "<":
            return value < threshold
        elif comparison == "<=":
            return value <= threshold
        return False
    
    def _execute_alert_actions(self, rule: Dict[str, Any], trigger_time: float):
        """
        Execute actions for triggered alert.
        """
        for action in rule["actions"]:
            if action.startswith("notify:"):
                self._send_notification(rule, trigger_time)
            elif action == "log":
                self._log_alert(rule, trigger_time)
            # Add more action types as needed
    
    def _send_notification(self, rule: Dict[str, Any], trigger_time: float):
        """
        Send notification for alert.
        """
        message = self._generate_alert_message(rule, trigger_time)
        print(f"Alert Notification: {message}")
        # In production, integrate with email, slack, etc.
    
    def _generate_alert_message(self, rule: Dict[str, Any], trigger_time: float):
        """
        Generate alert message.
        """
        metric_value = self.metrics.get_metric(rule["metric"]).get("value", 0.0)
        return (
            f"[{rule["severity"].upper()}] {rule["name"]}\n"
            f"Metric: {rule["metric"]}\n"
            f"Threshold: {rule["threshold"]}\n"
            f"Current Value: {metric_value}\n"
            f"Triggered At: {time.ctime(trigger_time)}"
        )
    
    def _log_alert(self, rule: Dict[str, Any], trigger_time: float):
        """
        Log alert to metrics system.
        """
        self.metrics.increment_counter("alerts.triggered", 1)
        self.metrics.record_metric("alerts.detail", self._generate_alert_message(rule, trigger_time))
    
    def _resolve_alert(self, alert_name: str):
        """
        Resolve an alert.
        """
        for alert_id in list(self.active_alerts):
            if alert_name in alert_id:
                self.active_alerts.remove(alert_id)
                self.metrics.increment_counter("alerts.resolved", 1)
                break
    
    def get_active_alerts(self) -> List[Dict[str, Any]]:
        """
        Get list of active alerts.
        """
        return [{
            "name": rule["name"],
            "metric": rule["metric"],
            "threshold": rule["threshold"],
            "severity": rule["severity"],
            "triggered_at": rule["triggered_at"]
        } for rule in self.alert_rules if rule.get("triggered", False)]
</think>

 # Comprehensive Performance Monitoring System

## Metric Types

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from typing import Dict, List, Optional, Any, Callable, Set
from datetime import datetime
import time
import threading
import json
from collections import defaultdict, deque


@dataclass
class MetricPoint:
    """Single metric observation with timestamp"""
    value: float
    timestamp: float = field(default_factory=time.time)
    tags: Dict[str, str] = field(default_factory=dict)


class MetricType(ABC):
    """Base class for metric types"""
    
    @abstractmethod
    def create(self, name: str, description: str = "") -> 'Metric':
        pass


class GaugeMetric(MetricType):
    """Gauge metric - current value"""
    
    def create(self, name: str, description: str = "") -> 'Metric':
        return Gauge(name, description)


class CounterMetric(MetricType):
    """Counter metric - cumulative value"""
    
    def create(self, name: str, description: str = "") -> 'Metric':
        return Counter(name, description)


class HistogramMetric(MetricType):
    """Histogram metric - distribution of values"""
    
    def create(self, name: str, description: str = "") -> 'Metric':
        return Histogram(name, description)


class TimerMetric(HistogramMetric):
    """Timer metric - measures durations"""
    
    def create(self, name: str, description: str = "") -> 'Metric':
        return Timer(name, description)


MetricTypeMap = {
    'gauge': GaugeMetric(),
    'counter': CounterMetric(),
    'histogram': HistogramMetric(),
    'timer': TimerMetric(),
}
```

## Core Metrics Implementation

```python
class Metric(ABC):
    """Base class for all metrics"""
    
    def __init__(
        self,
        name: str,
        description: str = "",
        tags: Dict[str, str] = None,
        registry: 'MetricRegistry' = None
    ):
        self.name = name
        self.description = description
        self.tags = tags or {}
        self.registry = registry
        self._lock = threading.RLock()
    
    @abstractmethod
    def update(self, value: float, timestamp: float = None) -> None:
        pass
    
    @abstractmethod
    def get(self) -> Dict[str, Any]:
        pass
    
    def with_tags(self, **kwargs) -> 'Metric':
        """Create a new metric with additional tags"""
        new_metric = self.__class__(
            self.name,
            self.description,
            {**self.tags, **kwargs},
            self.registry
        )
        return new_metric


class Gauge(Metric):
    """Gauge metric - current value"""
    
    def __init__(
        self,
        name: str,
        description: str = "",
        tags: Dict[str, str] = None,
        registry: 'MetricRegistry' = None
    ):
        super().__init__(name, description, tags, registry)
        self._value = 0.0
        self._history = deque(maxlen=1000)
    
    def update(self, value: float, timestamp: float = None) -> None:
        with self._lock:
            self._value = float(value)
            self._history.append((self._value, timestamp or time.time()))
    
    def get(self) -> Dict[str, Any]:
        with self._lock:
            return {
                'name': self.name,
                'description': self.description,
                'value': self._value,
                'tags': self.tags,
                'type': 'gauge',
                'history': list(self._history),
            }


class Counter(Metric):
    """Counter metric - cumulative value"""
    
    def __init__(
        self,
        name: str,
        description: str = "",
        tags: Dict[str, str] = None,
        registry: 'MetricRegistry' = None
    ):
        super().__init__(name, description, tags, registry)
        self._value = 0.0
        self._rates = {'one_minute': 0.0, 'five_minutes': 0.0, 'fifteen_minutes': 0.0}
        self._samples = deque(maxlen=60 * 60 * 15)  # 15 minutes worth at 1Hz
    
    def update(self, value: float, timestamp: float = None) -> None:
        with self._lock:
            if timestamp is None:
                timestamp = time.time()
            
            if not self._samples:
                self._value = value
            else:
                self._value = max(self._value, value)
            
            self._samples.append((self._value, timestamp))
    
    def increment(self, amount: float = 1.0) -> None:
        with self._lock:
            self._value += float(amount)
            if self._registry:
                self._registry._metrics[self.name] = self
    
    def get(self) -> Dict[str, Any]:
        with self._lock:
            return {
                'name': self.name,
                'description': self.description,
                'value': self._value,
                'tags': self.tags,
                'type': 'counter',
                'rates': self._rates,
            }


class Histogram(Metric):
    """Histogram metric - distribution of values"""
    
    def __init__(
        self,
        name: str,
        description: str = "",
        tags: Dict[str, str] = None,
        registry: 'MetricRegistry' = None
    ):
        super().__init__(name, description, tags, registry)
        self._values = []
        self._count = 0
        self._sum = 0.0
        self._min = float('inf')
        self._max = float('-inf')
        self._buckets = defaultdict(int)
        self._bucket_boundaries = [0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0, 10.0]
    
    def update(self, value: float, timestamp: float = None) -> None:
        with self._lock:
            self._values.append(value)
            self._count += 1
            self._sum += value
            self._min = min(self._min, value)
            self._max = max(self._max, value)
            
            for boundary in self._bucket_boundaries:
                if value < boundary:
                    self._buckets[boundary] += 1
                    break
    
    def get(self) -> Dict[str, Any]:
        with self._lock:
            return {
                'name': self.name,
                'description': self.description,
                'count': self._count,
                'sum': self._sum,
                'min': self._min,
                'max': self._max,
                'tags': self.tags,
                'type': 'histogram',
                'values': self._values[:1000],  # Limit to 1000 values
                'buckets': dict(self._buckets),
                'bucket_boundaries': self._bucket_boundaries,
            }


class Timer(Histogram):
    """Timer metric - measures durations"""
    
    def __init__(
        self,
        name: str,
        description: str = "",
        tags: Dict[str, str] = None,
        registry: 'MetricRegistry' = None
    ):
        super().__init__(name, description, tags, registry)
        self._bucket_boundaries = [0.001, 0.002, 0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1.0, 2.5, 5.0, 10.0]
    
    def record(self, duration: float) -> None:
        """Record a duration in seconds"""
        self.update(duration)


class MetricRegistry:
    """
    Metric registry manages metric lifecycle and provides lookup capabilities.
    """
    
    def __init__(self):
        self._metrics: Dict[str, Metric] = {}
        self._lock = threading.RLock()
        self._gc_interval = 600  # 10 minutes
        self._gc_thread = threading.Thread(target=self._garbage_collect, daemon=True)
        self._gc_thread.start()
    
    def _garbage_collect(self) -> None:
        """Periodically garbage collect metrics."""
        while True:
            try:
                with self._lock:
                    now = time.time()
                    metrics_to_remove = [
                        (name, metric)
                        for name, metric in self._metrics.items()
                        if not metric._samples
                    ]
                    for name, _ in metrics_to_remove:
                        del self._metrics[name]
            except Exception as e:
                print(f"Metric GC error: {e}")
            time.sleep(self._gc_interval)
    
    def create_metric(
        self,
        name: str,
        metric_type: str = 'gauge',
        description: str = "",
        tags: Dict[str, str] = None
    ) -> Metric:
        """
        Create and register a new metric.
        """
        metric_class = MetricTypeMap.get(metric_type, GaugeMetric())
        metric = metric_class.create(name, description, tags, self)
        self._metrics[name] = metric
        return metric
    
    def get(self, name: str) -> Optional[Metric]:
        """
        Get a metric by name.
        """
        with self._lock:
            return self._metrics.get(name)
    
    def get_all(self) -> Dict[str, Dict[str, Any]]:
        """
        Get all metrics as dictionary.
        """
        with self._lock:
            return {name: metric.get() for name, metric in self._metrics.items()}
    
    def update(self, name: str, value: float, timestamp: float = None) -> None:
        """
        Update a metric's value.
        """
        metric = self.get(name)
        if metric:
            metric.update(value, timestamp)
    
    def delete(self, name: str) -> bool:
        """
        Delete a metric.
        """
        with self._lock:
            if name in self._metrics:
                del self._metrics[name]
                return True
        return False
```

## Reporter Implementation

```python
class Reporter(ABC):
    """Base class for metric reporters"""
    
    @abstractmethod
    def report(self, metrics: Dict[str, Dict[str, Any]]) -> None:
        pass


class ConsoleReporter(Reporter):
    """Reports metrics to console"""
    
    def report(self, metrics: Dict[str, Dict[str, Any]]) -> None:
        print("=" * 60)
        print(f"Metrics Report - {datetime.now().isoformat()}")
        print("=" * 60)
        for metric_name, data in metrics.items():
            print(f"\nMetric: {metric_name}")
            for key, value in data.items():
                print(f"  {key}: {value}")
        print("=" * 60)


class StatsDReporter(Reporter):
    """
    Reports metrics to StatsD server.
    Uses UDP to send metrics.
    """
    
    def __init__(
        self,
        host: str = "localhost",
        port: int = 8125,
        prefix: str = "",
        max_queue_size: int = 1000
    ):
        self.host = host
        self.port = port
        self.prefix = prefix.rstrip(".") + "." if prefix else ""
        self.max_queue_size = max_queue_size
        self.queue = deque(maxlen=max_queue_size)
        self._lock = threading.RLock()
        self._socket = None
    
    def connect(self) -> None:
        """Connect to StatsD server."""
        if not self._socket:
            self._socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    def _send(self, message: str) -> None:
        """Send a message to StatsD."""
        if self._socket:
            try:
                self._socket.sendto(message.encode(), (self.host, self.port))
            except Exception as e:
                print(f"StatsD send error: {e}")
    
    def report(self, metrics: Dict[str, Dict[str, Any]]) -> None:
        """
        Report metrics to StatsD.
        """
        self.connect()
        for metric_name, data in metrics.items():
            full_name = f"{self.prefix}{metric_name}"
            if "value" in data:
                self._send(f"{full_name}:{data["value"]}|c")


class PrometheusReporter(Reporter):
    """
    Exposes metrics via HTTP in Prometheus format.
    """
    
    def __init__(self, handler: Callable = None):
        self.handler = handler or self.default_handler
        self.metrics = {}
        self._lock = threading.RLock()
    
    def default_handler(self, environ, start_response):
        """Default handler for HTTP requests."""
        start_response("200 OK", [
            ("Content-Type", "text/plain; version=0.0.4"),
            ("Cache-Control", "no-cache"),
        ])
        return [self._generate_metrics().encode()]
    
    def _generate_metrics(self) -> str:
        """Generate Prometheus-formatted metrics."""
        with self._lock:
            result = []
            for metric_name, data in self.metrics.items():
                if "value" in data:
                    result.append(f"{metric_name} {data["value"]}")
            return "\n".join(result)
    
    def report(self, metrics: Dict[str, Dict[str, Any]]) -> None:
        """
        Update metrics store.
        """
        with self._lock:
            self.metrics.update(metrics)


class HTTPServerReporter:
    """
    Runs an HTTP server to expose metrics.
    """
    
    def __init__(self, reporter: Reporter, host: str = "0.0.0.0", port: int = 9100):
        self.reporter = reporter
        self.host = host
        self.port = port
        self.server = None
        self.running = False
    
    def start(self) -> None:
        """Start the HTTP server."""
        from http.server import HTTPServer, BaseHTTPRequestHandler
        
        class MetricsHandler(BaseHTTPRequestHandler):
            def do_GET(self):
                self.reporter.report({})
                self.send_response(200)
                self.send_header("Content-Type", "text/plain; version=0.0.4")
                self.send_header("Cache-Control", "no-cache")
                self.end_headers()
                self.wfile.write(self.reporter._generate_metrics().encode())
            
            def log_message(self, *args):
                return  # Suppress default logging
        
        self.server = HTTPServer((self.host, self.port), MetricsHandler)
        self.running = True
        print(f"Metrics server running on http://{self.host}:{self.port}")
        self.server.serve_forever()


class FileReporter(Reporter):
    """
    Writes metrics to file periodically.
    """
    
    def __init__(self, filename: str, interval: int = 60):
        self.filename = filename
        self.interval = interval
        self._lock = threading.RLock()
        self._thread = threading.Thread(target=self._run, daemon=True)
        self._thread.start()
    
    def _run(self) -> None:
        """Run the file reporting loop."""
        while True:
            self.report(self._get_metrics())
            time.sleep(self.interval)
    
    def _get_metrics(self) -> Dict[str, Dict[str, Any]]:
        """Get metrics from registry."""
        from metrics import registry
        return registry.get_all()
    
    def report(self, metrics: Dict[str, Dict[str, Any]]) -> None:
        """
        Write metrics to file.
        """
        with self._lock:
            try:
                with open(self.filename, 'w') as f:
                    json.dump(metrics, f, indent=2)
            except Exception as e:
                print(f"File reporting error: {e}")
```

## Alerting System

```python
class AlertRule:
    """
    Defines a rule for triggering alerts based on metric conditions.
    """
    
    def __init__(
        self,
        name: str,
        metric: str,
        threshold: float,
        comparison: str = ">",
        duration: int = 60,
        severity: str = "warning",
        description: str = "",
        actions: Optional[List[str]] = None
    ):
        self.name = name
        self.metric = metric
        self.threshold = threshold
        self.comparison = comparison
        self.duration = duration
        self.severity = severity
        self.description = description
        self.actions = actions or []
        self.triggered_at = 0
        self.resolved = False
    
    def matches_condition(self, value: float) -> bool:
        """Check if metric value matches the condition."""
        if self.comparison == ">":
            return value > self.threshold
        elif self.comparison == ">=":
            return value >= self.threshold
        elif self.comparison == "<":
            return value < self.threshold
        elif self.comparison == "<=":
            return value <= self.threshold
        return False


class AlertManager:
    """
    Manages alert rules and triggers notifications when conditions met.
    """
    
    def __init__(self):
        self.rules: List[AlertRule] = []
        self.active_alerts: Dict[str, AlertRule] = {}
        self._lock = threading.RLock()
        self.check_interval = 10
    
    def add_rule(self, rule: AlertRule) -> None:
        """Add an alert rule."""
        self.rules.append(rule)
    
    def remove_rule(self, rule_name: str) -> bool:
        """Remove an alert rule by name."""
        with self._lock:
            for i, rule in enumerate(self.rules):
                if rule.name == rule_name:
                    del self.rules[i]
                    return True
        return False
    
    def check_rules(self, metrics: Dict[str, Dict[str, Any]]) -> None:
        """
        Check all alert rules against current metrics.
        """
        current_time = time.time()
        alerts_to_trigger = []
        alerts_to_resolve = []
        
        for rule in self.rules:
            metric_data = metrics.get(rule.metric)
            if not metric_data or "value" not in metric_data:
                continue
            
            value = metric_data["value"]
            
            if rule.matches_condition(value):
                if rule.name not in self.active_alerts:
                    alerts_to_trigger.append((rule, current_time))
            else:
                if rule.name in self.active_alerts:
                    alerts_to_resolve.append(rule.name)
        
        # Trigger new alerts
        for rule, trigger_time in alerts_to_trigger:
            rule.triggered_at = trigger_time
            rule.resolved = False
            self.active_alerts[rule.name] = rule
            self._execute_actions(rule, "trigger", value)
        
        # Resolve alerts
        for alert_name in alerts_to_resolve:
            if alert_name in self.active_alerts:
                self.active_alerts[alert_name].resolved = True
                self._execute_actions(self.active_alerts[alert_name], "resolve")
                del self.active_alerts[alert_name]
    
    def _execute_actions(self, rule: AlertRule, action_type: str, value: float = 0) -> None:
        """Execute alert actions."""
        for action in rule.actions:
            if action.startswith("notify:"):
                self._notify(action, rule, value)
            elif action == "log":
                self._log_alert(rule, value)
    
    def _notify(self, action: str, rule: AlertRule, value: float) -> None:
        """Send notification."""
        print(f"Alert [{rule.severity}]: {rule.name}")
        print(f"  Metric: {rule.metric}")
        print(f"  Threshold: {rule.threshold}")
        print(f"  Current value: {value}")
    
    def _log_alert(self, rule: AlertRule, value: float) -> None:
        """Log alert to metrics."""
        registry.update("alerts.triggered", 1)
        registry.update("alerts.detail", f"{rule.name}: {value}", tags={"rule": rule.name})


class AlertDashboard:
    """
    Web interface for managing alerts and viewing alert history.
    """
    
    def __init__(self, alert_manager: AlertManager):
        self.alert_manager = alert_manager
        self.history = []
        self._lock = threading.RLock()
    
    def get_active_alerts(self) -> List[Dict[str, Any]]:
        """Get active alerts."""
        with self._lock:
            return [{
                "name": alert.name,
                "metric": alert.metric,
                "threshold": alert.threshold,
                "severity": alert.severity,
                "value": alert.value,
                "triggered_at": alert.triggered_at
            } for alert in self.alert_manager.active_alerts.values()]
    
    def acknowledge_alert(self, alert_name: str) -> bool:
        """Acknowledge an alert."""
        with self._lock:
            for alert in self.alert_manager.active_alerts.values():
                if alert.name == alert_name:
                    alert.acknowledged = True
                    return True
        return False
    
    def get_alert_history(self, limit: int = 50) -> List[Dict[str, Any]]:
        """Get alert history."""
        with self._lock:
            return self.history[:limit]


class AlertNotifier:
    """
    Handles alert notifications through various channels.
    """
    
    def __init__(self):
        self.channels = {
            'console': ConsoleNotifier(),
            'email': EmailNotifier(),
            'slack': SlackNotifier(),
            'pagerduty': PagerDutyNotifier()
        }
    
    def register_channel(self, name: str, notifier: Callable) -> None:
        """Register a new notification channel."""
        self.channels[name] = notifier
    
    def notify(
        self,
        alert: AlertRule,
        value: float,
        severity: str = "warning"
    ) -> None:
        """Notify about an alert."""
        message = self._create_alert_message(alert, value, severity)
        for channel in self.channels.values():
            channel.send(message)
    
    def _create_alert_message(
        self,
        alert: AlertRule,
        value: float,
        severity: str
    ) -> str:
        """Create a formatted alert message."""
        return json.dumps({
            "alert_name": alert.name,
            "metric": alert.metric,
            "threshold": alert.threshold,
            "current_value": value,
            "severity": severity,
            "timestamp": datetime.now().isoformat(),
            "triggered": True
        })


class NotificationChannel(ABC):
    """Base class for notification channels."""
    
    @abstractmethod
    def send(self, message: str) -> bool:
        pass


class ConsoleNotifier(NotificationChannel):
    """Sends alerts to console."""
    
    def send(self, message: str) -> bool:
        print(f"ALERT: {message}")
        return True


class EmailNotifier(NotificationChannel):
    """Sends alerts via email."""
    
    def __init__(self, smtp_host: str, port: int = 587):
        self.smtp_host = smtp_host
        self.port = port
    
    def send(self, message: str) -> bool:
        # Email implementation
        return True


class SlackNotifier(NotificationChannel):
    """Sends alerts to Slack."""
    
    def __init__(self, webhook_url: str):
        self.webhook_url = webhook_url
    
    def send(self, message: str) -> bool:
        # Slack implementation
        return True


class PagerDutyNotifier(NotificationChannel):
    """Sends alerts to PagerDuty."""
    
    def __init__(self, api_key: str, service_key: str):
        self.api_key = api_key
        self.service_key = service_key
    
    def send(self, message: str) -> bool:
        # PagerDuty implementation
        return True
```

## Visualization and Dashboards

```python
class MetricsVisualizer:
    """
    Visualizes metrics using various plotting libraries.
    """
    
    def __init__(self):
        self.figures = {}
        self._lock = threading.RLock()
    
    def plot_metric(
        self,
        metric_name: str,
        metrics_data: Dict[str, Dict[str, Any]],
        duration: int = 3600
    ) -> None:
        """
        Plot a single metric over time.
        """
        import matplotlib.pyplot as plt
        import numpy as np
        from datetime import datetime
        
        with self._lock:
            data = metrics_data.get(metric_name)
            if not data or "history" not in data:
                return
            
            timestamps = []
            values = []
            for val, ts in data["history"]:
                timestamps.append(ts)
                values.append(val)
            
            plt.figure(figsize=(12, 6))
            plt.plot(timestamps, values, label=metric_name)
            plt.xlabel("Time")
            plt.ylabel("Value")
            plt.title(f"{metric_name} - {datetime.fromtimestamp(timestamps[-1])}")
            plt.grid(True)
            plt.legend()
            plt.tight_layout()
            
            self.figures[metric_name] = plt.gcf()
    
    def show_plots(self) -> None:
        """Show all plots."""
        for fig in self.figures.values():
            fig.show()


class RealtimeDashboard:
    """
    Real-time metrics dashboard using web technologies.
    """
    
    def __init__(self, metrics_registry, update_interval=2):
        self.registry = metrics_registry
        self.update_interval = update_interval
        self._lock = threading.RLock()
        self._running = False
        self._thread = None
    
    def start(self):
        """Start the dashboard."""
        self._running = True
        self._thread = threading.Thread(target=self._run, daemon=True)
        self._thread.start()
        print("Realtime dashboard started.")
    
    def stop(self):
        """Stop the dashboard."""
        self._running = False
    
    def _run(self):
        """Run the dashboard loop."""
        from flask import Flask, jsonify
        
        app = Flask(__name__)
        
        @app.route("/metrics")
        def get_metrics():
            return jsonify(self.registry.get_all())
        
        @app.route("/")
        def index():
            return "Realtime Metrics Dashboard"
        
        print(f"Access dashboard at http://localhost:5000")
        app.run(port=5000)


class GrafanaDashboard:
    """
    Integration with Grafana for advanced visualization.
    """
    
    def __init__(
        self,
        grafana_url: str,
        api_key: str,
        folder: str = "Metrics",
        dashboard_name: str = "Application Metrics"
    ):
        self.grafana_url = grafana_url
        self.api_key = api_key
        self.folder = folder
        self.dashboard_name = dashboard_name
    
    def create_dashboard(self, metrics: Dict[str, Dict[str, Any]]) -> Dict[str, Any]:
        """
        Create a new Grafana dashboard with metrics.
        """
        import requests
        
        headers = {
            "Authorization": f"Bearer {self.api_key}",
            "Content-Type": "application/json"
        }
        
        dashboard = {
            "dashboard": {
                "title": self.dashboard_name,
                "panels": self._create_panels(metrics),
                "description": "Automatically generated metrics dashboard"
            },
            "folderId": self._get_folder_id(headers),
            "overwrite": True
        }
        
        response = requests.post(
            f"{self.grafana_url}/api/dashboards/db",
            headers=headers,
            json=dashboard
        )
        
        if response.status_code == 200:
            return response.json()
        else:
            print(f"Grafana API error: {response.text}")
            return {}
    
    def _get_folder_id(self, headers) -> int:
        """Get folder ID for metrics."""
        response = requests.get(
            f"{self.grafana_url}/api/folders",
            headers=headers
        )
        if response.status_code == 200:
            for folder in response.json()["folders"]:
                if folder["title"] == self.folder:
                    return folder["id"]
        return 0
    
    def _create_panels(self, metrics: Dict[str, Dict[str, Any]]) -> List[Dict[str, Any]]:
        """Create panel configuration for each metric."""
        panels = []
        for i, (metric_name, data) in enumerate(metrics.items()):
            panels.append({
                "gridPos": {"h": 3, "w": 12, "x": 0, "y": i * 3},
                "id": i + 1,
                "title": metric_name,
                "type": "singlestat",
                "overrides": [],
                "options": {"showThresholdLabels": True, "showThresholdMarkers": True},
                "pluginVersion": "",
                "targets": [{
                    "datasource": "Prometheus",
                    "expr": metric_name,
                    "instant": True,
                    "interval": "",
                    "legendFormat": metric_name,
                    "refId": "A"
                }]
            })
        return panels
```

## Advanced Features

```python
class MetricSampler:
    """
    Samples metrics at different rates for different precision levels.
    """
    
    def __init__(self):
        self.samplers = {
            'high': SamplingRate(1.0, 1),
            'medium': SamplingRate(0.1, 10),
            'low': SamplingRate(0.01, 100),
        }
        self.lock = threading.RLock()
    
    def sample_metric(
        self,
        metric: Metric,
        rate: str = 'medium'
    ) -> Optional[float]:
        """
        Sample a metric at specified rate.
        """
        sampler = self.samplers.get(rate, self.samplers['medium'])
        if random.random() < sampler.rate:
            with self.lock:
                return metric._value
        return None


class SamplingRate:
    """Defines sampling rate parameters."""
    
    def __init__(self, rate: float, window: int):
        self.rate = rate
        self.window = window


class MetricSamplerManager:
    """
    Manages multiple metric sampling strategies.
    """
    
    def __init__(self):
        self.sampling_rules: Dict[str, SamplingRule] = {}
        self.lock = threading.RLock()
    
    def add_sampling_rule(
        self,
        metric_pattern: str,
        rate: str = 'medium',
        window: int = 60
    ) -> None:
        """
        Add a sampling rule for metrics matching a pattern.
        """
        rule = SamplingRule(metric_pattern, rate, window)
        self.sampling_rules[metric_pattern] = rule
    
    def get_sampling_rate(self, metric_name: str) -> float:
        """
        Get sampling rate for a metric.
        """
        for pattern, rule in self.sampling_rules.items():
            if pattern in metric_name:
                return rule.get_rate()
        return 1.0  # Default - sample all


class SamplingRule:
    """Defines a sampling rule with pattern matching."""
    
    def __init__(self, pattern: str, rate: str, window: int):
        self.pattern = pattern
        self.rate = rate
        self.window = window
    
    def get_rate(self) -> float:
        """Get the sampling rate value."""
        return {'high': 1.0, 'medium': 0.1, 'low': 0.01}.get(self.rate, 0.1)
    
    def matches(self, metric_name: str) -> bool:
        """Check if metric name matches the pattern."""
        return pattern in metric_name


class MetricAnomalyDetector:
    """
    Detects anomalies in metrics using statistical methods.
    """
    
    def __init__(self):
        self.models = {}
        self.lock = threading.RLock()
    
    def detect_anomalies(self, metrics: Dict[str, Dict[str, Any]]) -> Dict[str, List[Dict[str, Any]]]:
        """
        Detect anomalies in metrics.
        """
        anomalies = defaultdict(list)
        
        for metric_name, data in metrics.items():
            if "history" not in data:
                continue
            
            self._fit_or_update_model(metric_name, data)
            current_anomalies = self._detect_metric_anomalies(metric_name, data)
            anomalies[metric_name].extend(current_anomalies)
        
        return dict(anomalies)
    
    def _fit_or_update_model(self, metric_name: str, data: Dict[str, Any]) -> None:
        """Fit or update the anomaly detection model for a metric."""
        if metric_name not in self.models:
            self.models[metric_name] = self._create_default_model()
        
        model = self.models[metric_name]
        # Update model with new data
        model.update(data["history"])
    
    def _create_default_model(self) -> 'AnomalyDetectionModel':
        """Create a default anomaly detection model."""
        from statsmodels.tsa.holtwinters import ExponentialSmoothing
        return EWMAModel(alpha=0.2)
    
    def _detect_metric_anomalies(
        self,
        metric_name: str,
        data: Dict[str, Any]
    ) -> List[Dict[str, Any]]:
        """Detect anomalies for a single metric."""
        anomalies = []
        model = self.models.get(metric_name)
        
        if model and "history" in data:
            for value, timestamp in data["history"]:
                is_anomaly = model.detect(value)
                if is_anomaly:
                    anomalies.append({
                        "timestamp": timestamp,
                        "value": value,
                        "metric": metric_name,
                        "type": "anomaly"
                    })
        
        return anomalies


class AnomalyDetectionModel(ABC):
    """Base class for anomaly detection models."""
    
    @abstractmethod
    def update(self, data: List[Tuple[float, float]]) -> None:
        pass
    
    @abstractmethod
    def detect(self, value: float) -> bool:
        pass


class EWMAModel(AnomalyDetectionModel):
    """
    Exponentially Weighted Moving Average (EWMA) model
    for anomaly detection.
    """
    
    def __init__(self, alpha: float = 0.2, threshold: float = 3.0):
        self.alpha = alpha
        self.threshold = threshold
        self.c_current = 0.0
        self.sigma = 0.0
        self._lock = threading.RLock()
    
    def update(self, data: List[Tuple[float, float]]) -> None:
        """Update the model with new data."""
        for value, _ in data:
            self._update_stats(value)
    
    def _update_stats(self, value: float) -> None:
        """Update model statistics."""
        with self._lock:
            if self.c_current == 0:
                self.c_current = value
                self.sigma = 0
            else:
                self.c_current = self.alpha * value + (1 - self.alpha) * self.c_current
                self.sigma = self.alpha * (value - self.c_current) ** 2 + (1 - self.alpha) * self.sigma
    
    def detect(self, value: float) -> bool:
        """Detect if a value is an anomaly."""
        with self._lock:
            if self.sigma == 0:
                return False
            
            ewma = self.c_current
            current_std = self.sigma ** 0.5
            
            if current_std == 0:
                return False
            
            z_score = (value - ewma) / current_std
            return abs(z_score) > self.threshold


class ZScoreModel(AnomalyDetectionModel):
    """
    Z-score based anomaly detection model.
    Requires window of historical data.
    """
    
    def __init__(self, window: int = 60, threshold: float = 3.0):
        self.window = window
        self.threshold = threshold
        self.values = deque(maxlen=window)
        self.mean = 0.0
        self.variance = 0.0
        self._lock = threading.RLock()
    
    def update(self, data: List[Tuple[float, float]]) -> None:
        """Update the model with new data."""
        with self._lock:
            for value, _ in data:
                self.values.append(value)
            
            if len(self.values) >= self.window:
                self._update_statistics()
    
    def _update_statistics(self) -> None:
        """Recalculate mean and variance."""
        values = list(self.values)
        n = len(values)
        if n == 0:
            self.mean = 0.0
            self.variance = 0.0
            return
        
        new_mean = sum(values) / n
        self.variance = sum((x - new_mean) ** 2 for x in values) / n
    
    def detect(self, value: float) -> bool:
        """Detect if a value is an anomaly using Z-score."""
        with self._lock:
            if self.variance <= 0:
                return False
            
            current_mean = self.mean
            current_std = self.variance ** 0.5
            
            z_score = (value - current_mean) / current_std
            return abs(z_score) > self.threshold


class HoltWintersModel(AnomalyDetectionModel):
    """
    Holt-Winters exponential smoothing model
    for time series anomaly detection.
    """
    
    def __init__(
        self,
        alpha: float = 0.5,
        beta: float = 0.1,
        gamma: float = 0.1,
        threshold: float = 3.0
    ):
        self.alpha = alpha
        self.beta = beta
        self.gamma = gamma
        self.threshold = threshold
        self.level = 0.0
        self.trend = 0.0
        self.seasonal = {}
        self._lock = threading.RLock()
        self._initialized = False
    
    def update(self, data: List[Tuple[float, float]]) -> None:
        """Update the model with new data."""
        timestamps = [t for _, t in data]
        values = [v for v, _ in data]
        
        with self._lock:
            if not self._initialized:
                self._initialize(values, timestamps)
            else:
                self._update(values, timestamps)
    
    def _initialize(self, values: List[float], timestamps: List[float]) -> None:
        """Initialize the model parameters."""
        n = len(values)
        self.level = values[0]
        self.trend = values[1] - values[0]
        
        # Simple seasonal initialization (if we have enough data)
        if n > 365:
            for i in range(24):
                self.seasonal[i] = values[i]
        
        self._initialized = True
    
    def _update(self, values: List[float], timestamps: List[float]) -> None:
        """Update the model parameters."""
        for value, timestamp in zip(values, timestamps):
            self._smooth(value, timestamp)
    
    def _smooth(self, value: float, timestamp: float) -> None:
        """Smooth the metric value."""
        # This is a simplified version - full implementation would
        # handle seasonality and trends more carefully
        self.level = self.alpha * value + (1 - self.alpha) * (self.level + self.trend)
        self.trend = self.beta * (self.level - self.level_prev) + (1 - self.beta) * self.trend
        self.level_prev = self.level
    
    def detect(self, value: float) -> bool:
        """Detect anomalies using the model."""
        with self._lock:
            if not self._initialized:
                return False
            
            predicted = self.level + self.trend
            residual = value - predicted
            
            # Calculate standard deviation of residuals
            # (In practice, you'd track this more carefully)
            std_dev = max(1.0, self.gamma * abs(residual) + (1 - self.gamma) * self.std_dev_prev)
            self.std_dev_prev = std_dev
            
            if std_dev == 0:
                return False
            
            z_score = residual / std_dev
            return abs(z_score) > self.threshold


class MachineLearningModel(AnomalyDetectionModel):
    """
    Machine learning-based anomaly detection.
    Uses isolation forest or similar algorithms.
    """
    
    def __init__(self, n_estimators: int = 100, contamination: float = 0.05):
        self.n_estimators = n_estimators
        self.contamination = contamination
        self.model = None
        self._lock = threading.RLock()
        self._trained = False
    
    def update(self, data: List[Tuple[float, float]]) -> None:
        """Update the model with new data."""
        features = np.array([[v] for v, _ in data])
        
        with self._lock:
            if not self._trained:
                self._train(features)
            else:
                self._retrain(features)
    
    def _train(self, features: np.ndarray) -> None:
        """Train the model."""
        from sklearn.ensemble import IsolationForest
        self.model = IsolationForest(
            n_estimators=self.n_estimators,
            contamination=self.contamination,
            behaviour='new'
        )
        self.model.fit(features)
        self._trained = True
    
    def _retrain(self, features: np.ndarray) -> None:
        """Retrain the model with new data."""
        self._train(features)
    
    def detect(self, value: float) -> bool:
        """Detect anomaly using the machine learning model."""
        with self._lock:
            if not self._trained:
                return False
            
            prediction = self.model.predict([[value]])
            return prediction[0] == -1


class AnomalyNotifier:
    """
    Notifies about detected anomalies.
    """
    
    def __init__(self, alert_manager: AlertManager):
        self.alert_manager = alert_manager
    
    def notify_anomalies(self, anomalies: Dict[str, List[Dict[str, Any]]]) -> None:
        """
        Notify about detected anomalies.
        """
        for metric_name, anomaly_list in anomalies.items():
            for anomaly in anomaly_list:
                self._notify_single_anomaly(metric_name, anomaly)
    
    def _notify_single_anomaly(self, metric_name: str, anomaly: Dict[str, Any]) -> None:
        """Notify about a single anomaly."""
        message = (
            f"ANOMALY DETECTED!\n"
            f"Metric: {metric_name}\n"
            f"Value: {anomaly["value"]}\n"
            f"Timestamp: {datetime.fromtimestamp(anomaly["timestamp"])}\n"
            f"Type: {anomaly["type"]}"
        )
        self.alert_manager.add_rule(AlertRule(
            name=f"anomaly-{metric_name}-{int(anomaly["timestamp"])}",
            metric=metric_name,
            threshold=anomaly["value"],
            comparison=">",
            severity="critical",
            description=f"Anomaly detected in {metric_name}",
            actions=["notify:console", "log"]
        ))
```

## Integration and Examples

```python
class MetricsService:
    """
    Central metrics service combining registry, reporters, and alerting.
    """
    
    def __init__(self):
        self.registry = MetricRegistry()
        self.reporters: List[Reporter] = []
        self.alert_manager = AlertManager()
        self.visualizer = MetricsVisualizer()
        self.anomaly_detector = MetricAnomalyDetector()
        self.notifier = AnomalyNotifier(self.alert_manager)
    
    def add_reporter(self, reporter: Reporter) -> None:
        """Add a metrics reporter."""
        self.reporters.append(reporter)
    
    def record_metric(self, name: str, value: float, tags: Dict[str, str] = None) -> None:
        """Record a metric value."""
        metric = self.registry.create_metric(name, tags=tags)
        metric.update(value)
        
        # Notify reporters
        for reporter in self.reporters:
            reporter.report(self.registry.get_all())
    
    def create_metric(
        self,
        name: str,
        metric_type: str = 'gauge',
        description: str = "",
        tags: Dict[str, str] = None
    ) -> Metric:
        """Create a new metric."""
        return self.registry.create_metric(name, metric_type, description, tags)
    
    def get_metric(self, name: str) -> Optional[Metric]:
        """Retrieve a metric."""
        return self.registry.get(name)
    
    def get_all_metrics(self) -> Dict[str, Dict[str, Any]]:
        """Get all metrics as dictionary."""
        return self.registry.get_all()
    
    def start_reporting(self, interval: int = 10) -> None:
        """Start periodic reporting."""
        def report_loop():
            while True:
                for reporter in self.reporters:
                    reporter.report(self.registry.get_all())
                time.sleep(interval)
        
        threading.Thread(target=report_loop, daemon=True).start()
    
    def run_dashboard(self) -> None:
        """Start metrics dashboard."""
        self.visualizer.plot_all(self.registry.get_all())
        RealtimeDashboard(self.registry).start()


# Example Usage
if __name__ == "__main__":
    # Initialize metrics service
    metrics_service = MetricsService()
    
    # Add reporters
    metrics_service.add_reporter(ConsoleReporter())
    metrics_service.add_reporter(StatsDReporter())
    metrics_service.add_reporter(PrometheusReporter())
    
    # Create some metrics
    request_latency = metrics_service.create_metric(
        "http.request.latency",
        "Request latency in milliseconds",
        {"service": "api", "endpoint": "/v1/data"}
    )
    
    error_rate = metrics_service.create_metric(
        "http.error.rate",
        "Error rate percentage",
        {"service": "api"},
        "counter"
    )
    
    # Register alert rules
    metrics_service.alert_manager.add_rule(AlertRule(
        name="high_latency",
        metric="http.request.latency",
        threshold=500,
        comparison=">",
        duration=60,
        severity="warning",
        description="Request latency exceeds 500ms",
        actions=["notify:console", "log"]
    ))
    
    metrics_service.alert_manager.add_rule(AlertRule(
        name="high_errors",
        metric="http.error.rate",
        threshold=5,
        comparison=">",
        duration=300,
        severity="critical",
        description="Error rate exceeds 5%",
        actions=["notify:email", "notify:slack"]
    ))
    
    # Simulate some metrics data
    import random
    import time
    
    print("Starting metrics simulation...")
    for i in range(100):
        latency = random.uniform(200, 600)
        error = random.random() * 10
        
        metrics_service.record_metric("http.request.latency", latency)
        metrics_service.record_metric("http.error.rate", error)
        
        time.sleep(0.1)
    
    # Run visualization
    metrics_service.visualizer.plot_all(metrics_service.get_all_metrics())
```

## Complete System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                      METRICS COLLECTION SYSTEM                                                                 │
├─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                                                 │
│  ┌───────────────────────────────────────────────────────────────────────────────────────────────────────────┐  │
│  │                                                  METRIC PRODUCTION                                        │  │
│  │                                                                                                             │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │  Application Code                                                                                   │  │  │
│  │  │     │                                                                                               │  │  │
│  │  │     ├─► Measure business metrics                                                                  │  │  │
│  │  │     ├─► Observe system state                                                                      │  │  │
│  │  │     └─► Track user behavior                                                                       │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                                                             │
│  │                                                  METRIC STORAGE                                             │
│  │                                                                                                             │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │  Metric Registry                                                                                    │  │  │
│  │  │     │                                                                                               │  │  │
│  │  │     ├─► Metric dictionary                                                                         │  │  │
│  │  │     ├─► Type-specific handlers                                                                  │  │  │
│  │  │     ├─► Tagging and filtering                                                                   │  │  │
│  │  │     └─► Lazy initialization                                                                   │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                                                             │
│  │                                                  METRIC TRANSPORT                                           │
│  │                                                                                                             │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │  Reporters                                                                                          │  │  │
│  │  │     │                                                                                               │  │  │
│  │  │     ├─► ConsoleReporter                                                                           │  │  │
│  │  │     ├─► StatsDReporter                                                                          │  │  │
│  │  │     ├─► PrometheusReporter                                                                    │  │  │
│  │  │     ├─► FileReporter                                                                          │  │  │
│  │  │     └─► DatabaseReporter                                                                    │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                                                             │
│  │                                                  ALERTING SYSTEM                                            │
│  │                                                                                                             │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │  Alert Manager                                                                                      │  │  │
│  │  │     │                                                                                               │  │  │
│  │  │     ├─► Rule engine                                                                               │  │  │
│  │  │     ├─► Condition evaluation                                                                    │  │  │
│  │  │     ├─► Triggering mechanism                                                                  │  │  │
│  │  │     └─► Action dispatcher                                                                   │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                                                             │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │  Notifier                                                                                           │  │  │
│  │  │     │                                                                                               │  │  │
│  │  │     ├─► Email notifications                                                                       │  │  │
│  │  │     ├─► Slack alerts                                                                            │  │  │
│  │  │     ├─► PagerDuty integration                                                                 │  │  │
│  │  │     └─► In-app notifications                                                                  │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                                                             │
│  │                                                  VISUALIZATION                                              │
│  │                                                                                                             │
│  │  ┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐  │  │
│  │  │  Metrics Visualizer                                                                                 │  │  │
│  │  │     │                                                                                               │  │  │
│  │  │     ├─► Real-time dashboards                                                                      │  │  │
│  │  │     ├─► Historical analysis                                                                     │  │  │
│  │  │     ├─► Anomaly highlighting                                                                  │  │  │
│  │  │     └─► Customizable views                                                                  │  │  │
│  │  └─────────────────────────────────────────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                                                             │
│  └───────────────────────────────────────────────────────────────────────────────────────────────────────────┘  │
│                                                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```
