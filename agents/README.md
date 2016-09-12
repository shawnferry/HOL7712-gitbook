# Adding an Agent Node

Everything we have done in puppet so far can be achieved by simply using `puppet apply` on a single node. We will walk through adding a second node to the puppet configuration.

### Steps Overview

1. Open a second window or tab and connect to the new agent
2. Install puppet[^1]
3. Modify SMF config\/server variable for puppet:agent
4. Test agent connection
5. Sign the agent certificate \(on master\)

[^1]: Completed prior to lab

