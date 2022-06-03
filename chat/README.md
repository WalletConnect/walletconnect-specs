# Chat API Overview

## Description

Chat API allows wallet users to message 1-on-1 with other wallet users in the WalletConnect network by using blockchain accounts as addresses to establish end-to-end messaging between peers.

## Context

Chat functionality would be present in wallets where you could search or add a new contact from their wallet address on-chain and start a one-to-one conversation with them.

It would resemble direct messaging (DM) currently present in many social media applications but it would be end-to-end encrypted and available across different wallet providers.

Additionally it could be recovered from a seed phrase to allow users to migrate wallet provider without losing their chat.

## Goals

A user can register their blockchain account to be discoverable in the public-key registry.

A user can invite another wallet user to chat with their blockchain account regardless of its discoverability.

A user can message another wallet user after inviting them into a chat thread that has been mutually approved.

## Architecture

- Invite URI ([doc](invite-uri.md))
- Client API ([doc](client-api.md))
- Discoverability ([doc](discoverability.md))
- Chat Invite ([doc](chat-invite.md))
- RPC Methods ([doc](rpc-methods.md))
