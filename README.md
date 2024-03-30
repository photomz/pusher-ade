# 📲 Pusher ADE

The (unofficial) Postman for Pusher

<p align="center">
<img src="public/pusher.svg" width="150" height="150" alt="Pusher ADE Logo">
</p>

<a href='https://github.com/photomz/pusher-ade'><img src='https://img.shields.io/github/stars/photomz/pusher-ade?style=social'></a>
<a href='https://pusher.com/docs'><img src='https://img.shields.io/badge/Pusher-Docs-blue'></a>

Pusher ADE monitors Pusher Channels events for API development and testing in real-time. No existing ADE (Postman, HTTPie, etc) supports Pusher's WebSockets protocol, so we made one.

It works for chat apps, dashboards, or any app needing live data. It uses Vue, Shadcn UI and Tailwind and subscribes to Pusher Channels for easy real-time communication. It's one `App.vue` file (!!) and _very hackable_ for your needs.

## Setup

**Install**

```bash
pnpm install
```

**Environment Variables**: Create a `.env` file from `.env.sample` and add your Pusher app credentials. You can find these in your [Pusher dashboard](https://dashboard.pusher.com/apps/).

```
VITE_PUSHER_KEY=YOUR_PUSHER_KEY
VITE_PUSHER_CLUSTER=YOUR_PUSHER_CLUSTER
```

Start with HMR

```bash
pnpm start
```
