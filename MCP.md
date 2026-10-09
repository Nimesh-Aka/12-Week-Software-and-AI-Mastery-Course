**Model Context Protocol**
![[Pasted image 20260927091419.png]]

1. **Why did you use MCP instead of directly calling the Jira API?**
“We could have directly integrated the Jira API, but MCP gave us a standardized tool interface between the AI system and Jira. It separated the AI logic from the Jira-specific implementation and made the Jira capabilities reusable by MCP-compatible clients.

2. **What exactly did you build?**
I worked with teammates to build an internal MCP server that exposed Jira functionality as tools. The MCP server handled the tool definitions and input parameters, validated requests, and then called the Jira APIs to retrieve the required engineering data. The AI client could discover and invoke these tools through MCP.

3. **What is an MCP Server ?**

An **MCP server exposes capabilities to an MCP client**.
The client doesn't need to know the internal implementation, It only needs to know:
> “This server provides a `search_issues` tool.”

4. **What is an MCP Client?**
The MCP client is the component that connects an AI application to MCP servers. It discovers the capabilities exposed by the server and can request or invoke them on behalf of the AI workflow.

5. ⭐ What are MCP Resources?
Tools represent executable operations, while resources represent data or contextual information that can be exposed to the client. In our Jira use case, the main interaction was around tools for retrieving Jira information and performing operations.

6. ⭐ How does an MCP request actually flow?
The user request first reaches the AI application. The model determines that Jira information is required and selects the appropriate MCP tool. The MCP client sends the tool invocation to the Jira MCP server. The server validates the arguments and calls the Jira API. The resulting data is returned through MCP to the client, and the AI uses that data to generate the final response.


7. ⭐ How does the model know which Jira tool to use?

The MCP server exposes tool definitions including things like
- tool name
- description
- input schema
- parameters
to LLM.

8. ⭐ Why not expose the entire Jira API?
We didn't need to expose the entire Jira API. We focused on the operations required for the scrum workflow. This reduced unnecessary complexity, made the tools easier for the model to understand, and gave us better control over what the AI could access or execute

9. ⭐ How did you handle authentication with Jira?


10. ⭐ How is MCP different from an API?
A REST API is an interface provided by a service such as Jira. MCP is a protocol designed to standardize how AI applications interact with external tools and contextual data. In our case, the Jira MCP server acted as an AI-friendly layer over the Jira APIs.

11. ⭐ MCP vs function calling?
Function calling is a model capability where the model can request execution of a defined function. MCP is a protocol for standardizing how AI applications discover and interact with external tools and resources. MCP can therefore provide a reusable tool integration layer rather than defining every integration directly inside one AI application.

12. ⭐ How would you secure a Jira MCP server?
I would secure the MCP server using strong authentication, authorization at the tool level, strict input validation, secure secret management, and audit logging. I would also apply the principle of least privilege so the MCP server only has the Jira permissions it actually needs.