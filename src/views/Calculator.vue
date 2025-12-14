<template>
  <div class="calculator-container">
    <NavBar />

    <main class="calculator-main">
      <div class="calculator-wrapper">
        <!-- Calculator Card -->
        <mdui-card class="calculator-card">
          <!-- Display area -->
          <div class="display-section">
            <!-- History panel -->
            <mdui-collapse class="history-collapse" variant="basic">
              <mdui-collapse-item
                :open="history.length > 0"
                style="width: 100%;">
                <div slot="header" class="history-header">
                  <mdui-icon name="history" style="margin-right: 0.5rem;"></mdui-icon>
                  <span>歷史</span>
                </div>
                <div class="history-list">
                  <div
                    v-for="(item, index) in history"
                    :key="index"
                    class="history-item"
                    @click="recallHistory(item)"
                  >
                    <span v-html="renderLatex(item.expr)"></span> = <span v-html="renderLatex(item.res)"></span>
                  </div>
                </div>
              </mdui-collapse-item>
            </mdui-collapse>

            <!-- Expression display with visual cursor -->
            <div class="expression-display-container">
              <label class="display-label">表達式</label>
              <div
                class="expression-display-area"
                ref="expressionDisplayRef"
                @click="handleExpressionClick"
                tabindex="0"
              >
                <span v-html="renderLatex(expressions[currentLine], cursorPosition)"></span>
              </div>
            </div>

            <!-- Multi-line expression display for simultaneous equations -->
            <div v-if="expressions.length > 1" class="simultaneous-container">
              <div
                v-for="(expr, index) in expressions"
                :key="index"
                class="simultaneous-line"
                :class="{ 'active-line': index === currentLine }"
              >
                <span class="line-number">{{ index + 1 }}.</span>
                <div
                class="expression-display-area"
                v-html="renderLatex(expr, index === currentLine ? cursorPosition : -1)"
                v-if="index !== currentLine"
              >
              </div>
              <div
                class="expression-display-area"
                v-else
                tabindex="0"
                @click="handleExpressionClick"
              >
                <span v-html="renderLatex(expr, cursorPosition)"></span>
              </div>
              </div>
            </div>

            <mdui-text-field
              class="result-field"
              :value="result"
              readonly
              label="結果"
              variant="filled"
            ></mdui-text-field>
          </div>

          <!-- 11x4 keypad layout (11 columns x 4 rows = 44 buttons) -->
          <div class="keypad-section">
            <div class="keypad-grid">
              <!-- Row 1 -->
              <mdui-button class="calc-btn func-btn" @click="appendFunction('\\sin(')" variant="elevated">sin</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFunction('\\cos(')" variant="elevated">cos</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFunction('\\tan(')" variant="elevated">tan</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFunction('\\ln(')" variant="elevated">ln</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="insertLog" variant="elevated">log_a(x)</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFunction('√(')" variant="elevated">√</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFunction('∫')" variant="elevated">∫</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('7')" variant="elevated">7</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('8')" variant="elevated">8</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('9')" variant="elevated">9</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('/')" variant="elevated">÷</mdui-button>

              <!-- Row 2 -->
              <mdui-button class="calc-btn func-btn" @click="appendFunction('∂/∂x')" variant="elevated">∂/∂x</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="insertLimit" variant="elevated">lim</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="insertSigma" variant="elevated">∑</mdui-button>
              <mdui-button class="calc-btn const-btn" @click="appendValue('π')" variant="elevated">π</mdui-button>
              <mdui-button class="calc-btn const-btn" @click="appendValue('e')" variant="elevated">e</mdui-button>
              <mdui-button class="calc-btn var-btn" @click="appendValue('x')" variant="elevated">x</mdui-button>
              <mdui-button class="calc-btn var-btn" @click="appendValue('y')" variant="elevated">y</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('4')" variant="elevated">4</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('5')" variant="elevated">5</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('6')" variant="elevated">6</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('*')" variant="elevated">×</mdui-button>

              <!-- Row 3 -->
              <mdui-button class="calc-btn var-btn" @click="appendValue('∞')" variant="elevated">∞</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('|x|')" variant="elevated">|x|</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('^')" variant="elevated">x^y</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('!')" variant="elevated">x!</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('°')" variant="elevated">°</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendValue('nPr')" variant="elevated">nPr</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('(')" variant="elevated">(</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('1')" variant="elevated">1</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('2')" variant="elevated">2</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('3')" variant="elevated">3</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('-')" variant="elevated">−</mdui-button>

              <!-- Row 4 -->
              <mdui-button class="calc-btn op-btn" @click="appendValue(')')" variant="elevated">)</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFraction" variant="elevated">a/b</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendFunction('solve(')" variant="elevated">方程</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="clearAll" variant="elevated">C</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="deleteLastFunction" variant="elevated">⌫</mdui-button>
              <mdui-button class="calc-btn var-btn" @click="appendValue('i')" variant="elevated">i</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="insertNewLine" variant="elevated">↵</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('0')" variant="elevated">0</mdui-button>
              <mdui-button class="calc-btn num-btn" @click="appendValue('.')" variant="elevated">.</mdui-button>
              <mdui-button class="calc-btn func-btn" @click="appendValue('=')" variant="elevated">=</mdui-button>
              <mdui-button class="calc-btn op-btn" @click="appendValue('+')" variant="elevated">+</mdui-button>
            </div>
          </div>
        </mdui-card>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, nextTick, watch } from 'vue';
import 'mdui/components/button.js';
import 'mdui/components/card.js';
import 'mdui/components/text-field.js';
import 'mdui/components/icon.js';
import 'mdui/components/tooltip.js';
import 'mdui/components/menu.js';
import 'mdui/components/menu-item.js';
import 'mdui/components/dropdown.js';
import 'mdui/components/collapse.js';
import 'mdui/components/collapse-item.js';
import 'mdui/components/dialog.js';
import 'mdui/components/list.js';
import 'mdui/components/list-item.js';
import NavBar from '@/components/NavBar.vue';
import katex from 'katex';
import 'katex/dist/katex.min.css';

// State for calculator
const expression = ref('');
const expressions = ref<string[]>(['']); // For simultaneous equations
const currentLine = ref(0); // Currently active line
const result = ref('');
const history = ref<{expr: string, res: string}[]>([]);
const cursorPosition = ref(0);
const expressionDisplayRef = ref<HTMLElement | null>(null);

// Utility function to insert a string at a specific index
const insertAt = (originalString: string, index: number, stringToInsert: string) => {
  if (index > originalString.length) {
    return originalString + stringToInsert;
  }
  return originalString.substring(0, index) + stringToInsert + originalString.substring(index);
};

// Function to render LaTeX
const renderLatex = (text: string, currentCursorPosition: number = -1) => {
  // First, handle the cursor by splitting the text
  let textBeforeCursor = '';
  let textAfterCursor = '';
  let cursorHtml = '';
  
  if (currentCursorPosition !== -1 && currentCursorPosition <= text.length) {
    textBeforeCursor = text.substring(0, currentCursorPosition);
    textAfterCursor = text.substring(currentCursorPosition);
    cursorHtml = '<span style="color: var(--md3-primary); font-weight: bold;">▮</span>';
  } else {
    textBeforeCursor = text;
  }

  try {
    // Render the parts separately
    let renderedBefore = textBeforeCursor.replace(/\$(.*?)\$/g, (match, p1) => {
      return katex.renderToString(p1, { throwOnError: false, trust: true });
    });
    
    let renderedAfter = textAfterCursor.replace(/\$(.*?)\$/g, (match, p1) => {
      return katex.renderToString(p1, { throwOnError: false, trust: true });
    });
    
    // Handle display math
    renderedBefore = renderedBefore.replace(/\$\$(.*?)\$\$/g, (match, p1) => {
      return katex.renderToString(p1, { throwOnError: false, displayMode: true, trust: true });
    });
    
    renderedAfter = renderedAfter.replace(/\$\$(.*?)\$\$/g, (match, p1) => {
      return katex.renderToString(p1, { throwOnError: false, displayMode: true, trust: true });
    });

    return renderedBefore + cursorHtml + renderedAfter;
  } catch (e) {
    console.error('KaTeX rendering error:', e);
    return text + cursorHtml; // Return original text with cursor on error
  }
};

// Append value to expression at cursor position
const appendValue = (val: string) => {
  if (val === '=') {
    // For simultaneous equations, evaluate the system
    if (expressions.value.length > 1) {
      // Combine all expressions with system separator
      let combinedExpression = expressions.value.join(' AND ');
      if (combinedExpression) {
        // Store the calculation in history
        const newHistoryItem = { expr: combinedExpression, res: combinedExpression }; // Placeholder result
        history.value.unshift(newHistoryItem);
        if (history.value.length > 5) { // Keep only last 5 calculations
          history.value.pop();
        }
      }

      // For now, just show the combined expression as the result since we don't have a math solver
      result.value = combinedExpression;

      // Reset to single line
      expressions.value = [''];
      currentLine.value = 0;
    } else {
      // For single line expressions
      if (expression.value) {
        // Store the calculation in history
        const newHistoryItem = { expr: expression.value, res: expression.value }; // Placeholder result
        history.value.unshift(newHistoryItem);
        if (history.value.length > 5) { // Keep only last 5 calculations
          history.value.pop();
        }
      }

      // For now, just show the expression as the result since we don't have a math parser
      result.value = expression.value;
      expression.value = '';
    }
  } else if (val === '°') {
    const currentExpr = expressions.value[currentLine.value];
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + '$^\\circ$' + end;
    cursorPosition.value += '$^\\circ$'.length;
  } else if (val === '|x|') {
    const currentExpr = expressions.value[currentLine.value];
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + '$\\left|  \\right|$' + end;
    cursorPosition.value += '$\\left| '.length; // Place cursor inside
  } else if (val === 'π') {
    const currentExpr = expressions.value[currentLine.value];
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + '$\\pi$' + end;
    cursorPosition.value += '$\\pi$'.length;
  } else if (val === '∞') {
    const currentExpr = expressions.value[currentLine.value];
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + '$\\infty$' + end;
    cursorPosition.value += '$\\infty$'.length;
  } else if (val === 'nPr') {
    const currentExpr = expressions.value[currentLine.value];
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + '$P(n,r)$' + end; // Using P(n,r) for now
    cursorPosition.value += '$P(n,r)$'.length;
  } else {
    // Insert at cursor position in the current line
    const currentExpr = expressions.value[currentLine.value];
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + val + end;
    cursorPosition.value += val.length;
  }

  // Update display content after modifying expression
  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Append function to expression at cursor position
const appendFunction = (func: string, type?: string) => {
  let functionText = '';
  if (type === 'matrix') {
    // Matrix function - adding proper matrix notation
    functionText = `matrix(`;
  } else if (type === 'combinatorics') {
    // Combinatorics functions
    functionText = `${func}(`;
  } else if (type === 'sigma') {
    // Sigma function
    functionText = `∑(`;
  } else if (func === '∫') {
    functionText = '$\\int$';
  }

  // Insert at cursor position in the current line
  const currentExpr = expressions.value[currentLine.value];
  const start = currentExpr.substring(0, cursorPosition.value);
  const end = currentExpr.substring(cursorPosition.value);
  expressions.value[currentLine.value] = start + functionText + end;
  cursorPosition.value += functionText.length;

  // Update display content after modifying expression
  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Clear all
const clearAll = () => {
  expressions.value = ['']; // Reset to single empty expression
  currentLine.value = 0; // Set current line to 0
  expression.value = ''; // Clear the main expression
  result.value = '';

  // Update display content after clearing
  nextTick(() => {
    updateExpressionDisplay();
  });
};



// Delete last function or element intelligently
const deleteLastFunction = () => {
  const expr = expressions.value[currentLine.value];
  if (expr.length === 0) return;

  let i = Math.min(cursorPosition.value, expr.length); // Cursor position, not index
  if (i === 0) return;

  const textBeforeCursor = expr.substring(0, i);

  // 1. Check for complete LaTeX math expressions first
  // Look for patterns like $...$ or $...$
  const mathPattern = /\$[^$]*\$/;
  const mathMatch = textBeforeCursor.match(mathPattern);
  if (mathMatch) {
    const matchedText = mathMatch[0];
    const newExpr = expr.substring(0, i - matchedText.length) + expr.substring(i);
    expressions.value[currentLine.value] = newExpr;
    cursorPosition.value = i - matchedText.length;
    nextTick(() => {
      updateExpressionDisplay();
    });
    return;
  }

  // 2. Check for specific LaTeX patterns (longest first)
  const latexPatterns = [
    '$\\frac{\\partial}{\\partial x}$',
    '$\\log_{a}(x)$',
    '$\\lim_{x \\to a}$',
    '$\\sum_{i=1}^{n}$',
    '$\\sin($',
    '$\\cos($',
    '$\\tan($',
    '$^\\circ$',
    '$\\left|$',
    '$\\right|$',
    '$\\pi$',
    '$\\infty$',
    '$P(n,r)$',
    '$\\int$',
    '$\\frac{}{}$',
  ];

  for (const pattern of latexPatterns) {
    if (textBeforeCursor.endsWith(pattern)) {
      const newExpr = expr.substring(0, i - pattern.length) + expr.substring(i);
      expressions.value[currentLine.value] = newExpr;
      cursorPosition.value = i - pattern.length;
      nextTick(() => {
        updateExpressionDisplay();
      });
      return;
    }
  }

  // 3. Existing parenthesis matching logic
  // Start from the character before the cursor position
  let charIndex = i - 1; // Convert cursor position to 0-based index
  if (charIndex < 0) return; // Should not happen if i > 0

  if (expr[charIndex] === ')') {
    let parenCount = 1;
    let j = charIndex - 1;
    while (j >= 0 && parenCount > 0) {
      if (expr[j] === ')') parenCount++;
      else if (expr[j] === '(') parenCount--;
      j--;
    }

    if (parenCount === 0) {
      let funcStart = j + 1;
      // Look backwards to find the start of the function name, including '\' for LaTeX commands
      while (funcStart > 0 && /[a-zA-Z_0-9\\]/.test(expr[funcStart - 1])) {
        funcStart--;
      }

      const newExpr = expr.substring(0, funcStart) + expr.substring(charIndex + 1);
      expressions.value[currentLine.value] = newExpr;
      cursorPosition.value = funcStart;
      nextTick(() => {
        updateExpressionDisplay();
      });
      return;
    }
  }

  // 4. If no specific pattern or parenthesis match, remove the single character
  const newExpr = expr.substring(0, charIndex) + expr.substring(charIndex + 1);
  expressions.value[currentLine.value] = newExpr;
  cursorPosition.value = charIndex;

  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Handle keyboard input
const handleKeyDown = (event: KeyboardEvent) => {
  const key = event.key;
  const currentExpr = expressions.value[currentLine.value];

  if (key.length === 1 && !event.ctrlKey && !event.metaKey && !event.altKey) { // Regular character input
    event.preventDefault();
    const start = currentExpr.substring(0, cursorPosition.value);
    const end = currentExpr.substring(cursorPosition.value);
    expressions.value[currentLine.value] = start + key + end;
    cursorPosition.value += key.length;
  } else if (key === 'Enter' || key === '=') {
    event.preventDefault();
    appendValue('=');
  } else if (key === 'Escape') {
    event.preventDefault();
    clearAll();
  } else if (key === 'Backspace') {
    event.preventDefault();
    deleteLastFunction(); // Use the intelligent delete function
  } else if (key === 'ArrowLeft') {
    event.preventDefault();
    cursorPosition.value = Math.max(0, cursorPosition.value - 1);
  } else if (key === 'ArrowRight') {
    event.preventDefault();
    cursorPosition.value = Math.min(currentExpr.length, cursorPosition.value + 1);
  } else if (key === 'p' && event.altKey) {
    event.preventDefault();
    appendValue('π');
  } else if (key === 'e' && event.altKey) {
    event.preventDefault();
    appendValue('e');
  }
  // Update the main expression ref to trigger re-render
  expression.value = expressions.value[currentLine.value];
};

// Function to select text in a field when clicked
const selectText = (event: Event) => {
  const target = event.target as HTMLInputElement;
  if (target && target.select) {
    target.select();
  }
};

// Function to recall a calculation from history
const recallHistory = (item: {expr: string, res: string}) => {
  expression.value = item.expr;
};

// Function to insert a new line for simultaneous equations
const insertNewLine = () => {
  // Add a new line to the expressions array
  expressions.value.push('');
  currentLine.value = expressions.value.length - 1; // Move to the new line
};

// Function to insert logarithm with custom base
const insertLog = () => {
  // Insert log template: log_base(number)
  const currentExpr = expressions.value[currentLine.value];
  const start = currentExpr.substring(0, cursorPosition.value);
  const end = currentExpr.substring(cursorPosition.value);
  const logTemplate = `$\\log_{a}(x)$`; // LaTeX for log with base
  expressions.value[currentLine.value] = start + logTemplate + end;
  cursorPosition.value += '$\\log_{'.length; // Position cursor at 'a'

  // Update display content after modifying expression
  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Function to insert limit
const insertLimit = () => {
  // Insert limit template: lim(x→a)expression
  const currentExpr = expressions.value[currentLine.value];
  const start = currentExpr.substring(0, cursorPosition.value);
  const end = currentExpr.substring(cursorPosition.value);
  const limitTemplate = `$\\lim_{x \\to a}$`; // LaTeX for limit
  expressions.value[currentLine.value] = start + limitTemplate + end;
  cursorPosition.value += '$\\lim_{'.length; // Position cursor at 'x'

  // Update display content after modifying expression
  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Function to insert sigma (summation)
const insertSigma = () => {
  // Insert sigma template: Σ(i=1 to n)
  const currentExpr = expressions.value[currentLine.value];
  const start = currentExpr.substring(0, cursorPosition.value);
  const end = currentExpr.substring(cursorPosition.value);
  const sigmaTemplate = `$\\sum_{i=1}^{n}$`; // LaTeX for sigma
  expressions.value[currentLine.value] = start + sigmaTemplate + end;
  cursorPosition.value += sigmaTemplate.length; // Position cursor after 'Σ'

  // Update display content after modifying expression
  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Function to append fraction with visual formatting
const appendFraction = () => {
  // Insert fraction template at cursor position: \frac{numerator}{denominator}
  const currentExpr = expressions.value[currentLine.value];
  const start = currentExpr.substring(0, cursorPosition.value);
  const end = currentExpr.substring(cursorPosition.value);
  const fractionTemplate = '$\\frac{}{}$';
  expressions.value[currentLine.value] = start + fractionTemplate + end;
  cursorPosition.value += '$\\frac{'.length; // Position cursor at the numerator

  // Update display content after modifying expression
  nextTick(() => {
    updateExpressionDisplay();
  });
};

// Function to open equation solver dialog
const openEquationSolver = () => {
  // Create a simple dialog to input equations
  const dialog = document.createElement('mdui-dialog');
  dialog.innerHTML = `
    <div slot="header">聯立方程求解</div>
    <div slot="content">
      <mdui-text-field id="eq1" label="方程 1" style="display: block; margin-bottom: 1rem;"></mdui-text-field>
      <mdui-text-field id="eq2" label="方程 2" style="display: block; margin-bottom: 1rem;"></mdui-text-field>
      <mdui-text-field id="eq3" label="方程 3 (可選)" style="display: block;"></mdui-text-field>
    </div>
    <div slot="actions">
      <mdui-button-dialog variant="text">取消</mdui-button-dialog>
      <mdui-button-dialog variant="filled">求解</mdui-button-dialog>
    </div>
  `;

  document.body.appendChild(dialog);
  dialog.open = true;

  dialog.addEventListener('closed', () => {
    document.body.removeChild(dialog);
  });
};

// Handle click on expression display to set cursor position
const handleExpressionClick = (event: MouseEvent) => {
  // Focus the display area so it can receive keyboard events
  const target = event.target as HTMLElement;
  if (target) {
    target.focus();
  }
};

// Watch for changes in current line to reset cursor position
watch(currentLine, (newLine) => {
  // Reset cursor position when switching lines
  cursorPosition.value = expressions.value[newLine]?.length || 0;
});

// Update expression display to reflect current expression value
const updateExpressionDisplay = () => {
  // Update the main expression field to match the current line
  if (expressions.value.length > 0) {
    expression.value = expressions.value[currentLine.value];
  }
};



// Format expression with visual representations for fractions and other elements
const formatExpression = (expr: string) => {
  // Replace fraction notation numerator/denominator with visual fraction
  let formatted = expr.replace(/([^\/\s]+)\/([^\/\s]+)/g, '<span class="fraction"><span class="numerator">$1</span><span class="fraction-bar">/</span><span class="denominator">$2</span></span>');

  // Replace other special mathematical notations if needed
  return formatted;
};

// Add event listener for keyboard input when component mounts
document.addEventListener('keydown', handleKeyDown);

// Clean up event listener
// Note: In a real app, you'd want to properly manage this lifecycle with onUnmounted
</script>

<style scoped>
/* Material Design 3 Expressive Color Scheme */
@import 'katex/dist/katex.min.css';

:root {
  --md3-primary: #6750A4;
  --md3-on-primary: #FFFFFF;
  --md3-primary-container: #EADDFF;
  --md3-on-primary-container: #21005D;
  --md3-secondary: #625B71;
  --md3-on-secondary: #FFFFFF;
  --md3-secondary-container: #E8DEF8;
  --md3-on-secondary-container: #1D192B;
  --md3-tertiary: #7D5260;
  --md3-on-tertiary: #FFFFFF;
  --md3-tertiary-container: #FFD8E4;
  --md3-on-tertiary-container: #31111D;
  --md3-surface: #FFFBFE;
  --md3-on-surface: #1C1B1F;
  --md3-surface-variant: #E7E0EC;
  --md3-on-surface-variant: #49454F;
  --md3-outline: #79747E;
  --md3-outline-variant: #CAC4D0;
  --md3-shadow: #000000;
  --md3-scrim: #000000;
  --md3-error: #B3261E;
  --md3-on-error: #FFFFFF;
  --md3-error-container: #F9DEDC;
  --md3-on-error-container: #410E0B;

  /* Custom theme colors based on reference */
  --theme-primary: #6750A4;
  --theme-on-primary: #FFFFFF;
  --theme-surface: #FFFFFF;
  --theme-on-surface: #111418;
  --theme-on-surface-variant: #617589;
  --theme-outline: #f0f2f4;
}

.calculator-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background-color: var(--md3-surface);
  color: var(--md3-on-surface);
  font-family: "Roboto", "Segoe UI", sans-serif;
}

.calculator-main {
  flex: 1;
  padding: 1.25rem 2.5rem;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.calculator-wrapper {
  width: 100%;
  max-width: 800px; /* Increased width for 11 columns */
}

.calculator-card {
  background: var(--md3-surface);
  border-radius: 16px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  border: 1px solid var(--md3-outline-variant);
}

.display-section {
  padding: 1.5rem;
  background: var(--md3-surface-variant);
  border-bottom: 1px solid var(--md3-outline-variant);
  min-height: 150px;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.expression-display-container {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.display-label {
  font-size: 0.85rem;
  color: var(--md3-on-surface-variant);
  margin-bottom: 0.25rem;
}

.expression-display-area {
  width: 100%;
  min-height: 2.5rem;
  padding: 0.75rem;
  border: 2px solid var(--md3-outline);
  border-radius: 8px;
  background-color: var(--md3-surface);
  font-size: 1.2rem;
  color: var(--md3-on-surface);
  overflow-x: auto;
  white-space: nowrap;
  text-align: left;
  cursor: text;
}

.expression-display-area:focus {
  outline: none;
  border-color: var(--md3-primary);
}

/* Visual cursor styling */
.cursor {
  color: var(--md3-primary); /* Set to primary theme color */
  /* animation: blink 1s infinite; Removed blinking as requested */
}

/* Style for the cursor character in the expression display */
.expression-display-area::selection {
  background-color: var(--md3-primary-container);
  color: var(--md3-on-primary-container);
}

.expression-display-area:focus {
  outline: 2px solid var(--md3-primary);
  outline-offset: 2px;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.2; }
}

/* Fraction styling */
.fraction {
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  margin: 0 0.25em;
  vertical-align: middle;
}

.numerator {
  border-bottom: 1px solid var(--md3-on-surface);
  padding-bottom: 0.1em;
}

.fraction-bar {
  line-height: 1;
  padding: 0.1em 0;
}

.denominator {
  padding-top: 0.1em;
}

/* Simultaneous equations container */
.simultaneous-container {
  width: 100%;
  border: 1px solid var(--md3-outline-variant);
  border-radius: 8px;
  padding: 0.5rem;
  background-color: color-mix(in srgb, var(--md3-primary) 5%, transparent);
  margin-top: 0.5rem;
}

.simultaneous-line {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
  padding: 0.25rem 0;
}

.simultaneous-line:last-child {
  margin-bottom: 0;
}

.simultaneous-line.active-line {
  background-color: color-mix(in srgb, var(--md3-primary) 10%, transparent);
  border-radius: 4px;
}

.line-number {
  font-size: 0.9rem;
  color: var(--md3-on-surface-variant);
  min-width: 1.5rem;
}


.formatted-expression-display {
  width: 100%;
  min-height: 2.5rem;
  padding: 0.75rem;
  border: 2px solid color-mix(in srgb, var(--md3-outline) 50%, transparent);
  border-radius: 8px;
  background-color: color-mix(in srgb, var(--md3-surface) 80%, transparent);
  font-size: 1.2rem;
  color: var(--md3-on-surface);
  overflow-x: auto;
  white-space: nowrap;
  text-align: left;
  margin-top: 0.5rem;
}

.result-field {
  width: 100%;
  --md-filled-text-field-container-color: var(--md3-surface-variant);
  --md-filled-text-field-label-text-color: var(--md3-primary);
  --md-filled-text-field-caret-color: var(--md3-primary);
  font-size: 1.4rem;
  font-weight: 500;
  text-align: right;
}

.history-collapse {
  margin-bottom: 1rem;
  --md-sys-color-primary: var(--md3-on-surface-variant);
  --md-sys-color-on-surface-variant: var(--md3-on-surface-variant);
}

.history-header {
  display: flex;
  align-items: center;
  font-size: 0.9rem;
  color: var(--md3-on-surface-variant);
}

.history-list {
  max-height: 100px;
  overflow-y: auto;
  padding: 0.5rem 0;
}

.history-item {
  padding: 0.25rem 0.5rem;
  cursor: pointer;
  font-size: 0.85rem;
  color: var(--md3-on-surface-variant);
  border-bottom: 1px solid var(--md3-outline-variant);
}

.history-item:hover {
  background-color: color-mix(in srgb, var(--md3-primary) 10%, transparent);
}

/* Standard 4-column keypad grid */
.keypad-grid {
  display: grid;
  grid-template-columns: repeat(11, 1fr);
  gap: 0.5rem;
  padding: 1rem;
  margin-bottom: 0.5rem;
}

/* Responsive design for keypad */
@media (max-width: 768px) {
  .calculator-main {
    padding: 1rem;
  }

  .display-section {
    padding: 1rem;
  }

  .calc-btn {
    min-height: 2.8rem;
    font-size: 0.85rem;
  }
}

/* Responsive design for calculator layout */
@media (max-width: 768px) {
  .calculator-main {
    padding: 1rem;
  }

  .calculator-wrapper {
    max-width: 100%;
  }

  .calc-btn {
    min-height: 2.8rem;
    font-size: 0.85rem;
  }
}
</style>