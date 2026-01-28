<xml xmlns="https://developers.google.com/blockly/xml">
  <block type="trade" x="20" y="20">
    <field name="MARKET_LIST">synthetic_index</field>
    <field name="SUBMARKET_LIST">random_index</field>
    <field name="SYMBOL_LIST">R_100</field>
    <field name="TRADETYPE_LIST">rise_fall</field>

    <statement name="INITIALIZATION">
      <block type="variables_set">
        <field name="VAR">stake</field>
        <value name="VALUE"><block type="math_number"><field name="NUM">0.35</field></block></value>
        <next>
          <block type="variables_set">
            <field name="VAR">rsi_period</field>
            <value name="VALUE"><block type="math_number"><field name="NUM">14</field></block></value>
            <next>
              <block type="variables_set">
                <field name="VAR">ema_period</field>
                <value name="VALUE"><block type="math_number"><field name="NUM">50</field></block></value>
                <next>
                  <block type="variables_set">
                    <field name="VAR">loss_streak</field>
                    <value name="VALUE"><block type="math_number"><field name="NUM">0</field></block></value>
                    <next>
                      <block type="variables_set">
                        <field name="VAR">max_loss_streak</field>
                        <value name="VALUE"><block type="math_number"><field name="NUM">3</field></block></value>
                        <next>
                          <block type="variables_set">
                            <field name="VAR">cooldown_ticks</field>
                            <value name="VALUE"><block type="math_number"><field name="NUM">5</field></block></value>
                          </block>
                        </next>
                      </block>
                    </next>
                  </block>
                </next>
              </block>
            </next>
          </block>
        </next>
      </block>
    </statement>

    <!-- MAIN LOGIC -->
    <statement name="SUBMARKET">
      <block type="controls_if">
        <value name="IF0">
          <block type="logic_compare">
            <field name="OP">LT</field>
            <value name="A"><block type="variables_get"><field name="VAR">loss_streak</field></block></value>
            <value name="B"><block type="variables_get"><field name="VAR">max_loss_streak</field></block></value>
          </block>
        </value>

        <statement name="DO0">
          <!-- BUY CONDITION -->
          <block type="controls_if">
            <value name="IF0">
              <block type="logic_and">
                <value name="A">
                  <block type="logic_compare">
                    <field name="OP">LT</field>
                    <value name="A">
                      <block type="technical_rsi"><value name="PERIOD"><block type="variables_get"><field name="VAR">rsi_period</field></block></value></block>
                    </value>
                    <value name="B"><block type="math_number"><field name="NUM">30</field></block></value>
                  </block>
                </value>
                <value name="B">
                  <block type="logic_compare">
                    <field name="OP">GT</field>
                    <value name="A"><block type="technical_last_price"/></value>
                    <value name="B"><block type="technical_ema"><value name="PERIOD"><block type="variables_get"><field name="VAR">ema_period</field></block></value></block></value>
                  </block>
                </value>
              </block>
            </value>

            <statement name="DO0">
              <block type="purchase">
                <field name="CONTRACT_TYPE">CALL</field>
                <value name="AMOUNT"><block type="variables_get"><field name="VAR">stake</field></block></value>
                <value name="DURATION"><block type="math_number"><field name="NUM">1</field></block></value>
                <field name="DURATION_UNIT">t</field>
                <next>
                  <block type="variables_set">
                    <field name="VAR">loss_streak</field>
                    <value name="VALUE"><block type="math_number"><field name="NUM">0</field></block></value>
                  </block>
                </next>
              </block>
            </statement>

            <next>
              <!-- SELL CONDITION -->
              <block type="controls_if">
                <value name="IF0">
                  <block type="logic_and">
                    <value name="A">
                      <block type="logic_compare">
                        <field name="OP">GT</field>
                        <value name="A"><block type="technical_rsi"><value name="PERIOD"><block type="variables_get"><field name="VAR">rsi_period</field></block></value></block></value>
                        <value name="B"><block type="math_number"><field name="NUM">70</field></block></value>
                      </block>
                    </value>
                    <value name="B">
                      <block type="logic_compare">
                        <field name="OP">LT</field>
                        <value name="A"><block type="technical_last_price"/></value>
                        <value name="B"><block type="technical_ema"><value name="PERIOD"><block type="variables_get"><field name="VAR">ema_period</field></block></value></block></value>
                      </block>
                    </value>
                  </block>
                </value>

                <statement name="DO0">
                  <block type="purchase">
                    <field name="CONTRACT_TYPE">PUT</field>
                    <value name="AMOUNT"><block type="variables_get"><field name="VAR">stake</field></block></value>
                    <value name="DURATION"><block type="math_number"><field name="NUM">1</field></block></value>
                    <field name="DURATION_UNIT">t</field>
                    <next>
                      <block type="variables_set">
                        <field name="VAR">loss_streak</field>
                        <value name="VALUE">
                          <block type="math_arithmetic">
                            <field name="OP">ADD</field>
                            <value name="A"><block type="variables_get"><field name="VAR">loss_streak</field></block></value>
                            <value name="B"><block type="math_number"><field name="NUM">1</field></block></value>
                          </block>
                        </value>
                      </block>
                    </next>
                  </block>
                </statement>
              </block>
            </next>
          </block>
        </statement>

        <!-- COOLDOWN -->
        <next>
          <block type="controls_repeat_ext">
            <value name="TIMES"><block type="variables_get"><field name="VAR">cooldown_ticks</field></block></value>
            <statement name="DO"><block type="wait_ticks"/></statement>
          </block>
        </next>
      </block>
    </statement>
  </block>
</xml>
