************************************************
Setting the Allowed Transaction Types (Optional)
************************************************

.. note::

    These instructions have been tested on Ubuntu 16.04 only.

By default, a validator accepts transactions from any transaction processor.
However, Sawtooth allows you to limit the types of transactions that can be
submitted.

This setting, ``sawtooth.validator.transaction_families``, improves the
Sawtooth network's security by ignoring any unrecognized transaction processors.
It is an on-chain setting, which means that the change is submitted on one node;
the other nodes in the network apply the settings change when they receive the
block with this transaction. Note that the
:doc:`Settings transaction processor <../transaction_family_specifications/settings_transaction_family>`
is required to handle on-chain configuration settings.

In this procedure, you will configure the validator network to limit the
accepted transaction types to those from this network's transaction processors
(as started in :doc:`systemd`).

.. important::

   For the environment described in this guide, you  **must** run this procedure
   on the same node that created the genesis block, because the ``sawset
   proposal create`` command requires the validator key that was generated on
   that node.

#. Open a terminal window on the "genesis node" (the Sawtooth node that created
   the genesis block in a previous procedure).

#. Use the ``sawset`` command to create and submit a batch of transactions that
   changes the allowed transaction types.

   * For PBFT:

     .. code-block:: console

        $ sudo sawset proposal create --key /etc/sawtooth/keys/validator.priv \
        sawtooth.validator.transaction_families='[{"family":"sawtooth_identity", "version":"1.0"}, {"family":"intkey", "version": "1.0"}, {"family":"sawtooth_settings", "version":"1.0"}]'
   * For PoET:

     .. code-block:: console

        $ sudo sawset proposal create --key /etc/sawtooth/keys/validator.priv \
        sawtooth.validator.transaction_families='[{"family":"sawtooth_identity", "version":"1.0"}, {"family":"intkey", "version": "1.0"}, {"family":"sawtooth_settings", "version":"1.0"}, {"family":"sawtooth_validator_registry", "version":"1.0"}]'

   This command sets ``sawtooth.validator.transaction_families`` to a JSON array
   that specifies the family name and version of the following transaction
   processors:

   * `sawtooth_identity` (Identity)
   * `intkey` (IntegerKey)
   * `sawtooth_settings` (Settings)
   * (PoET only) `sawtooth_validator_registry` (PoET Validator Registry)

   See :doc:`transaction family specification <../transaction_family_specifications>`
   for the family names and versions of all Sawtooth transaction processors.

#. Run the following command to check the setting change.

   .. code-block:: console

      $ sawtooth settings list

   The output should be similar to this example:

   * For PBFT:

     .. code-block:: console

        sawtooth.consensus.algorithm.name=sawtooth-pbft-engine
        sawtooth.consensus.algorithm.version=0.1.2
        sawtooth.consensus.pbft.peers=03e27504580fa15...
        sawtooth.consensus.pbft.block_duration=200
        sawtooth.consensus.pbft.checkpoint_period=100
        sawtooth.consensus.pbft.view_change_timeout=4000
        sawtooth.consensus.pbft.message_timeout=10
        sawtooth.consensus.pbft.max_log_size=1000
        sawtooth.publisher.max_batches_per_block: 200
        sawtooth.settings.vote.authorized_keys: 03e27504580fa15...
        sawtooth.validator.transaction_families: [{"family": "in...

   * For PoET:

     .. code-block:: console

        sawtooth.consensus.algorithm.name: PoET
        sawtooth.consensus.algorithm.version: 0.1
        sawtooth.poet.initial_wait_time: 15
        sawtooth.poet.key_block_claim_limit: 100000
        sawtooth.poet.report_public_key_pem: -----BEGIN PUBL...
        sawtooth.poet.target_wait_time: 15
        sawtooth.poet.valid_enclave_basenames: b785c58b77152cb...
        sawtooth.poet.valid_enclave_measurements: c99f21955e38dbb...
        sawtooth.poet.ztest_minimum_win_count: 100000
        sawtooth.publisher.max_batches_per_block: 200
        sawtooth.settings.vote.authorized_keys: 03e27504580fa15...
        sawtooth.validator.transaction_families: [{"family": "in...

#. You can also check the log file for the Settings transaction processor,
   ``/var/log/sawtooth/logs/settings-{xxxxxxx}-debug.log`` for a
   ``TP_PROCESS_REQUEST`` message. (Note that the Settings log file has a unique
   string in the file name.)

   The message will resemble this example:

   * For PBFT:

     .. code-block:: none

        [20:07:58.039 [MainThread] core DEBUG] received message of type: TP_PROCESS_REQUEST
        [20:07:58.190 [MainThread] handler INFO] Setting setting sawtooth.validator.transaction_families changed from None to [{"family": "intkey", "version": "1.0"}, {"family":"sawtooth_settings", "version":"1.0"}]'

   * For PoET:

     .. code-block:: none

        [20:07:58.039 [MainThread] core DEBUG] received message of type: TP_PROCESS_REQUEST
        [20:07:58.190 [MainThread] handler INFO] Setting setting sawtooth.validator.transaction_families changed from None to [{"family": "intkey", "version": "1.0"}, {"family":"sawtooth_settings", "version":"1.0"}, {"family":"sawtooth_validator_registry", "version":"1.0"}]'


.. Licensed under Creative Commons Attribution 4.0 International License
.. https://creativecommons.org/licenses/by/4.0/
