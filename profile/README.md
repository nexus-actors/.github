<div align="center">

# Nexus

### A typed actor system for PHP 8.5+ &nbsp; `WIP`

Type-safe actors, supervision trees, event sourcing, multi-process clustering.<br>
Erlang/OTP and Akka patterns — in the PHP you already know.

[Documentation](https://nexus-actors.github.io/nexus/) &middot; [Quick Start](https://nexus-actors.github.io/nexus/docs/getting-started/quick-start) &middot; [GitHub](https://github.com/nexus-actors/nexus)

</div>

---

```php
// Define messages
readonly class Ping {
    public function __construct(public ActorRef $replyTo) {}
}
readonly class Pong {}

// Define actor behavior
$pongActor = Behavior::receive(
    static fn(ActorContext $ctx, Ping $msg) => $msg->replyTo->tell(new Pong()) ?? Behavior::same(),
);

// Spawn and interact
$system = ActorSystem::create('my-app', new FiberRuntime());
$ref = $system->spawn(Props::fromBehavior($pongActor), 'pong');
$response = $ref->ask(fn(ActorRef $replyTo) => new Ping($replyTo), Duration::seconds(5));
// $response is Pong
```

---

### Key Features

- **Type-safe actors** — Generic `ActorRef<T>`, `Behavior<T>`, `Props<T>` with Psalm level 1 enforcement
- **Supervision trees** — One-for-one, all-for-one, exponential backoff with custom exception deciders
- **Event sourcing & durable state** — Persist events or state snapshots with automatic crash recovery
- **Pluggable runtimes** — Write once, run on PHP Fibers (dev) or Swoole coroutines (production)
- **Multi-process clustering** — Consistent hash ring, location-transparent `RemoteActorRef`, Unix socket transport
- **Custom Psalm plugin** — 7 actor-specific static analysis rules catch concurrency bugs at compile time

### Packages

| Package | Description |
|---|---|
| **[nexus](https://github.com/nexus-actors/nexus)** | Monorepo & meta-package |
| [core](https://github.com/nexus-actors/core) | Actors, behaviors, supervision, mailboxes |
| [runtime-fiber](https://github.com/nexus-actors/runtime-fiber) | PHP Fiber runtime |
| [runtime-swoole](https://github.com/nexus-actors/runtime-swoole) | Swoole coroutine runtime |
| [runtime-step](https://github.com/nexus-actors/runtime-step) | Deterministic test runtime |
| [app](https://github.com/nexus-actors/app) | Application bootstrap with PSR-11 |
| [serialization](https://github.com/nexus-actors/serialization) | Message serialization |
| [persistence](https://github.com/nexus-actors/persistence) | Event sourcing & durable state |
| [persistence-dbal](https://github.com/nexus-actors/persistence-dbal) | DBAL persistence store |
| [persistence-doctrine](https://github.com/nexus-actors/persistence-doctrine) | Doctrine persistence store |
| [cluster](https://github.com/nexus-actors/cluster) | Multi-process clustering |
| [cluster-swoole](https://github.com/nexus-actors/cluster-swoole) | Swoole cluster transport |
| [psalm](https://github.com/nexus-actors/psalm) | Actor-specific Psalm plugin |

### Get Started

```bash
composer require nexus-actors/nexus
```

```php
NexusApp::create('my-app')
    ->actor('greeter', Props::fromBehavior($greeterBehavior))
    ->onStart(function (ActorSystem $system) {
        $system->spawn(Props::fromBehavior($greeterBehavior), 'greeter')
            ->tell(new Greet('world'));
    })
    ->run(new FiberRuntime());
```

---

<div align="center">

Backed by [Monadial](https://monadial.com) &middot; MIT License

</div>
